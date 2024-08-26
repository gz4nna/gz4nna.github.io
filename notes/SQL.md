# 前言

T-SQL 主要分爲三種類型的陳述式
- DDL
- DML
- DCL

# DML

DML(Data Manipulation Language)

數據操作語言,用於操作和處理數據庫中的數據,主要涉及增刪改查等操作

## 常見用法

### select

```sql
SELECT
	*
FROM
	Employees
```

如果有 `where` 條件,放在 `group by` 或 `order by` 之前

如果有複雜的查詢條件,在使用 EF Core 時,我傾向與分步執行后在内存中整合結果,在程序層處理數據

但是在sql中更加常用的方式是,將結果的查詢放在最外層,然後通過子查詢或公共表達式(Common Table Expressions)來不斷降低每一步的條件複雜度,也就是説將數據處理放在了數據庫層

子查詢示例

```sql
SELECT
	*
FROM
	Employees
WHERE
	EmployeeID IN (
	    SELECT
		    EmployeeID
	    FROM
		    Orders
	    WHERE
		    OrderDate > '2023-01-01'
	)
```

公共表達式示例

```sql
WITH RecentOrders AS (
	SELECT
		EmployeeID,
		COUNT(*) AS OrderCount
	FROM
		Orders
	WHERE
		OrderDate > '2023-01-01'
	GROUP BY
		EmployeeID
)

SELECT 
	e.FirstName,
	e.LastName,
	r.OrderCount
FROM
	Employees e
	JOIN
	RecentOrders r
	ON
		e.EmployeeID = r.EmployeeID
```

有關 select 的更多使用範例可以查看[微軟文檔](https://learn.microsoft.com/zh-tw/sql/t-sql/queries/select-examples-transact-sql)

### insert

```sql
INSERT INTO 
	Employees (FirstName, Department)
VALUES 
	('Jane', 'Marketing');
```

插入表的時候可以選擇只插入部分列

例如上面例子中,Employees 表中可以有不只兩個列的屬性,但是在顯式指定了插入數據是屬於具體的哪些列后,完全少插入一些數據(在不違反完整性唯一性等等條件下)

**注意**

儘管有以下定義

```sql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse and Microsoft Fabric

INSERT [INTO] { database_name.schema_name.table_name | schema_name.table_name | table_name }
    [ ( column_name [ ,...n ] ) ]  
    {   
      VALUES ( { NULL | expression } )  
      | SELECT <select_criteria>  
    }  
    [ OPTION ( <query_option> [ ,...n ] ) ]  
[;]
```

但事實上,不同的數據庫產品可能會有自己的 SQL 方言,所以强制添加而不是省略引數 into 是更好的做法.後續示例也都基於這個規則

有關 insert 的更多使用範例可以查看[微軟文檔](https://learn.microsoft.com/zh-tw/sql/t-sql/statements/insert-transact-sql)
### update

```sql
UPDATE
	Employees
SET
	Department = 'Marketing',
	FirstName = 'Jane'
WHERE
	LastName = 'Doe'
```

### delete

```sql
DELETE FROM
	Employees
WHERE
	LastName = 'Doe'
```

# DDL

DDL(Data Definition Language)

數據定義語言,用於定義和管理數據庫中的結構,主要在修改表結構而不是内容時使用

## 常見用法

### create

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    Department NVARCHAR(50)
)
```

### alter

```sql
ALTER TABLE
	Employees
ADD
	Email NVARCHAR(100)
```

```sql
ALTER TABLE
	Employees
ALTER COLUMN
	LastName NVARCHAR(100)
```

```sql
ALTER TABLE
	Employees
DROP COLUMN
	Department
```

### drop

```sql
DROP TABLE
	Employees
```

# DCL

DCL(Data Control Language)
數據控製語言,用於控制對數據庫的訪問權限,主要涉及用戶權限等

# 其他操作

若非一般的操作,基本上我偏向於在程序代碼中完成,但是也有必須使用SQL語句的情況

### merge

merge 是用於將數據插入,更新或刪除到目標表中的一種語句

常常會涉及源表和目標表的概念,并且可以在一個操作中完成複數個操作的功能

```sql
MERGE
INTO
	Employees AS Target
USING
	NewEmployees AS Source
ON
	Target.EmployeeID = Source.EmployeeID
WHEN
MATCHED
THEN
    UPDATE
    SET
	    Target.FirstName = Source.FirstName,
	    Target.LastName = Source.LastName,
	    Target.Department = Source.Department
WHEN
NOT MATCHED
THEN
    INSERT
	    (EmployeeID, FirstName, LastName, Department)
    VALUES
	    (Source.EmployeeID, Source.FirstName, Source.LastName, Source.Department)
WHEN
NOT MATCHED BY SOURCE
THEN
DELETE
```

而在 EF Core 中,類似的功能實現如下

```csharp
// 1. 查询所有匹配的记录
var matchedEmployees = context.Employees
    .Where(e => context.NewEmployees.Any(ne => ne.EmployeeID == e.EmployeeID))
    .ToList();

// 2. 更新匹配的记录
foreach (var employee in matchedEmployees)
{
    var newEmployee = context.NewEmployees
        .FirstOrDefault(ne => ne.EmployeeID == employee.EmployeeID);
    
    if (newEmployee != null)
    {
        employee.FirstName = newEmployee.FirstName;
        employee.LastName = newEmployee.LastName;
        employee.Department = newEmployee.Department;
    }
}

context.SaveChanges();

// 3. 插入未匹配的记录
var unmatchedNewEmployees = context.NewEmployees
    .Where(ne => !context.Employees.Any(e => e.EmployeeID == ne.EmployeeID))
    .ToList();

context.Employees.AddRange(unmatchedNewEmployees);
context.SaveChanges();

// 4. 删除不再匹配的记录
var employeesToDelete = context.Employees
    .Where(e => !context.NewEmployees.Any(ne => ne.EmployeeID == e.EmployeeID))
    .ToList();

context.Employees.RemoveRange(employeesToDelete);
context.SaveChanges();

```


### 索引

若數據表的規模較大,在查詢的時候需要花費的時間可能會漫長到無法等待,在列上建立索引可以加快查詢到速度,並有效避免遍歷全部記錄才能夠找到目標

數據表中的索引就相當於在原有的一列記錄上叠加了一個值從而達到優化目的

#### 聚集索引

在表中設置主鍵后,將會自動創建一個聚集索引

聚集索引將會決定表中數據的物流存儲順序,并且只能存在一個聚集索引

除了主鍵,範圍查詢也適合使用聚集索引

```sql
CREATE CLUSTERED INDEX
	IX_Employees_EmployeeID
ON
	Employees(EmployeeID)
```

#### 非聚集索引

非聚集索引像是對列數據創建了一張跳轉表,適合排序和分組

```sql
CREATE NONCLUSTERED INDEX
	IX_Employees_LastName
ON
	Employees(LastName)
```

#### 唯一索引

唯一索引僅確保數據唯一,可以是聚集索引也可以是非聚集索引

```sql
CREATE UNIQUE INDEX
	IX_Employees_Email
ON
	Employees(Email);
```

#### 全文索引

#### XML索引\

#### 空間索引

#### 過濾索引

## 事務

## 函數


# 安裝使用

項目中也許會使用不同的數據庫,甚至可能同時涉及多個不同的數據庫.不管是開發還是維護,都有必要瞭解常見產品的基本使用

- SQL server
- 