
# 前期工作和設計

技術選型我選擇了 `MAUI` 作爲開發的框架, `SQLite` 作爲本地數據庫, `BootstrapBlazor` 作爲UI庫

這一套技術棧的搭配我已經不是第一次嘗試了, 不過這次的目的是爲了鞏固所以依舊沿用老一套方案

## 創建項目

創建時選擇模板 `.NET MAUI Blazor Application`, 目標框架使用 `.NET 8.0`

創建完成 `RebuildingHabitKit.MAUI` 后直接運行 `Android Emulator`, 一切正常后繼續下一步

## 本地數據庫

`HabitKit` 能夠由用戶創建一個"習慣"(Habit),並持續對其記錄完成情況, 因此我們必須擁有存儲數據的能力

### 安裝依賴

在 `Nuget` 中安裝以下依賴
- `Microsoft.EntityFrameworkCore.Sqlite`
- `Microsoft.EntityFrameworkCore.Tools`
- `Microsoft.EntityFrameworkCore.Design`

需要注意安裝的大版本必須和使用的 `.net` 大版本一致

### 全局引用

在 `Compoments/_Import.razor` 中添加引用語句可以令其成爲全局引用(global using), 避免繁雜的反復顯式引用

```csharp
@using Microsoft.EntityFrameworkCore
```

### 測試

通過使用簡單的測試驗證目前項目可用

#### 測試用模型

新建一個類庫 `RebuildingHabitKit.Model`, 和 `MAUI` 項目一致, 選擇 `.NET 8.0`

在 `RebuildingHabitKit.Model.Test` 資料夾下創建兩個類型

```csharp
namespace RebuildingHabitKit.Model.Test;

public class TestTable
{
    public List<TestRow>? Rows { get; set; }
}
```

```csharp
namespace RebuildingHabitKit.Model.Test;

public class TestRow
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
    public TestRow(int id, string name, string description)
    {
        Id = id;
        Name = name;
        Description = description;
        CreatedAt = DateTime.Now;
        UpdatedAt = DateTime.Now;
    }
}
```

爲了鏈接上數據庫,還需要額外一個

```csharp
namespace RebuildingHabitKit.Model.Test;

public class TestDbContext:DbContext
{
    public DbSet<TestRow> TestRows { get; set; }
    public DbSet<TestTable> TestTables { get; set; }

    public string DbPath { get; }

    public TestDbContext()
    {
        var folder = Environment.SpecialFolder.LocalApplicationData;
        var path = Environment.GetFolderPath(folder);
        DbPath = System.IO.Path.Join(path, "testDatabase.db");
    }

    protected override void OnConfiguring(DbContextOptionsBuilder dbContextOptionsBuilder)
        => dbContextOptionsBuilder.UseSqlite($"Data Source={DbPath}");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<TestRow>().ToTable("TestRows");
        modelBuilder.Entity<TestTable>().ToTable("TestTables");
    }
}
```

通過在 `MauiProgram.cs` 中添加這句,可以將 `DbContext` 注入到任意頁面中使用

```csharp
var builder = MauiApp.CreateBuilder();
builder.Services.AddDbContext<TestDbContext>();
```