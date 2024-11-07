# <折光编年史:reflaction>开发总结

十月底 `TapTap` 举办了 `聚光灯GameJam` 活动,我应小新邀请,在活动中作为一名程序参与制作了 <折光编年史:reflaction>

<折光> 是一款卡通风塔防游戏,玩家需要放置防御塔来保护基地不受敌人的攻击.防御塔主要使用光线对敌人攻击,另外设有辅助性质的塔,可提供反射和折射等功能

前期我主要负责了编写敌人相关的逻辑,后期负责整个项目的代码维护和功能添加.现在活动告一段落,遂回想一下这次经历所带来的收获

## 程序方面

### 从自己代码中总结

#### 优雅致死,追求完美可能会损失效率

举个例子,在刚开始的阶段,我负责的是所有敌人的相关逻辑编写.出于 cs 的编码思维,我很自然地一开始就使用一个接口来描述所有敌人的基本属性和基本行为,然后继承自该接口,实现了一个敌人的基本类,再着手开始各种敌人的开发

在整个开发的后期,我这样的做法确实带来了很大的便利性.得益于我在设计阶段就考虑得很充分,后续不管增加什么样的敌人,大部分都只需要继承已有的类,然后重写几行特定的行为就可以了.甚至有些敌人可以在不修改代码而只是调整预制体参数设置的情况下完成

但是在开发前期,由于我需要把精力放在思考"如何设计才是更合理的"这个问题上面,导致另一位程序完成许多功能时我还依旧在各种方案之间取舍,结果最后被评价了"过于模式化"┭┮﹏┭┮但其实我完全可以每个类单独开发,重复的代码 CV 一下也花不了几秒钟,对性能也没有什么影响,至于优化完全可以在 `PostJam` 的阶段完成,那么为什么不呢?完全只是因为我觉得不优雅😭

上面的情况反映出来的**起步阶段的效率丢失**是一方面,另一方面是,`GameJam` 本身是一种生命周期极短的活动,一个游戏需要在几天之内完成,完成后也不一定会继续开发.就比如这次的<折光>,在策划案中的内容明显比实际开发的内容更加丰富,并且直到发布在 `TapTap` 上的前夕,成员们都还有继续开发后续内容的打算.然而现实是,直到现在也没有着手开始对游戏进行完善.于是乎,<折光>彻底变成了一上线就"死掉"的项目,我彻彻底底地做了一次**过度设计**

我明显地感觉到,自己学习的东西越多,越有一种想要在一开始就达成"完美设计"的强烈冲动,但是必须得考虑是否值得.短期且不需要重写的东西还是适当将重心放在追求速度上吧~

#### 使用 `StartCoroutine` 开启协程达到延时效果

在编码时碰到一个需求:我要在指定时间间隔后执行一些逻辑

在 `.NET` 程序中,一般会采用 `Task.Delay` 方法结合 `await/async` 来实现这个功能,但是 `Task` 实际上是采用线程实现的,而在 `Unity` 中,尽管存在对 `Task` 的支持,但是在非主线程上获取物体是会出问题的,所以这时候需要换用协程来处理

例如以下代码:

```csharp
public bool Attackable 
{ 
    get => attackable; 
    set
    {
        attackable = value;
        if(attackable == false) StartCoroutine(ResetAttackableAfterDelay(AttackInterval));
    }
}

private IEnumerator ResetAttackableAfterDelay(float delay)
{
    yield return new WaitForSeconds(delay);
    attackable = true;
}
```

使用 **`StartCoroutine` 方法开启一个协程**,在 `Unity` 运行到这个地方时,会在每一帧检查迭代器 `ResetAttackableAfterDelay` 中的 `WaitForSeconds` 方法是否完成,完成后才会执行语句 `attackable = true;`,从而达到本处延时但不影响其他部分的正常运行(不会阻塞主线程)

对应的 `.NET` 版本应该是这样

```csharp
private Task resetTask;

public bool Attackable
{
    get => attackable;
    set
    {
        attackable = value;
        if (attackable == false) resetTask = ResetAttackableAfterDelay(AttackInterval);
    }
}

private async Task ResetAttackableAfterDelay(int delay)
{
    await Task.Delay(delay);
    attackable = true;
}
```

#### 使用 `PlayerPrefs` 存储临时数据

`PlayerPrefs` 是 `Unity` 提供的一个轻量级数据存储系统,适合用来**保存简单的数据**.

我一开始并不知道有它的存在,于是在单个场景中使用单例实现数据的临时存放.但是后续出现了要在多个场景之间进行变量传递的需求,此时我发现 `PlayerPrefs` 是一个很好的工具,就像一个随时可用的字典

- 存储数据
```csharp
PlayerPrefs.SetString(string key, string value);
PlayerPrefs.Save();
```

- 获取数据
```csharp
PlayerPrefs.GetString(string key, string defaultValue = "");
```

- 删除数据
```csharp
PlayerPrefs.DeleteKey(string key);
```

甚至它可以在应用退出的时候自动保存数据,所以条件有限的时候还可以勉强用来做持久化.更多内容参考[官方文档](https://docs.unity.cn/cn/current/ScriptReference/PlayerPrefs.html)的描述

#### 使用 `JSON` 来保存数据

和"困觉"的小伙伴们一起开发的时候,曾经尝试过将数据存放在 `csv` 中,而这次我选择了使用 `JSON` 来存放关卡信息.这样的选择主要考虑了几方面:
- 希望可以在游戏发布后方便地修改数据(自带老金)
- 没有尝试过在 `Unity` 中使用 `JSON`

首先,我将关卡信息放在 `Unity` 默认存放数据用的 `Assets/Resources` 文件夹下,这样可以确保使用 `Resources.Load` 方法获取文件

接下来,将持久化文件放在各个平台对应的默认存放位置,这个位置可以使用 `Application.persistentDataPath` 获取到

至此,完成了数据在目标平台的存放,读取也很简单

首先使用 `File.ReadAllText` 方法读取文件,再使用 `JsonUtility.FromJson\<T>` 方法转换成相应的类型,这部分的操作和 .NET 程序中是完全一样的

总体上来说,在 `Unity` 中使用 `JSON` 来存放信息的**操作没有特殊的变化**.需要注意的是,虽然我在使用的时候一切正常,但是 `Resources.Load` 这个方法被描述为"不支持直接加载 `JSON`"(我使用的泛型类型为 `TextAsset`,估计把它当作 `txt` 去读了)

更加推荐的方式是将文件放入 `Assets/StreamingAssets` 文件夹,然后这样使用

```csharp
string path = System.IO.Path.Combine(Application.streamingAssetsPath, "文件名");

UnityWebRequest request = UnityWebRequest.Get(path);
yield return request.SendWebRequest();

string jsonContent = request.downloadHandler.text;
// 解析
```

`UnityWebRequest` 是异步操作,需要用[上文](#使用-startcoroutine-开启协程达到延时效果)提到的方式去开启协程来调用.这种方式效率较低,一般用于**需要跨平台并确保发布时不易更改的静态文件**,比如这次的关卡信息就很合适
### 从他人代码中学习

#### 使用 `LineRender` 制作射线

防御塔的主要攻击手段是发射光线到敌人身上,这部分的实现小伙伴采用了 `LineRender` 这个类型来绘制光线,这恰好是我不了解的知识

`LineRender` 作为组件挂载到 `GameObject` 上,简单的绘制激光主要需要设置:

- 线条宽度
```csharp
lineRenderer.startWidth = 0.1f;
lineRenderer.endWidth = 0.1f;
```

- 线条颜色
```csharp
lineRenderer.material = new Material(Shader.Find("Unlit/Color"));
lineRenderer.material.color = laserColor;
```

- 起点和终点
```csharp
lineRenderer.SetPosition(0, laserOrigin.position);
lineRenderer.SetPosition(1, hit.point);
```

可以参考[官方文档](https://docs.unity.cn/cn/current/Manual/class-LineRenderer.html)中对此的相关描述