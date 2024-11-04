在 Web API 中使用 Swagger

创建一个新的 ASP.NET Core WebAPI 项目时，会同步使用 Swagger 作为 API 文档，我们可以通过在管线中自定义添加 Swagger 中间件时的实现，来达到对 Swagger 内容或执行逻辑的一些定制化支持

Swagger 默认会添加在 Program.cs 中，自动生成的相关代码如下

```csharp
builder.Services.AddSwaggerGen();

...

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
	app.UseSwaggerUI();
}
```

通过修改服务的添加来将默认的 Swagger 改为我们想要的样子，首先将原本的 AddSwaggerGen 封装在自己的方法中

```csharp
public static void InitSwagger(this IServiceCollection services)
{
    services.AddSwaggerGen();
}
```

根据 AddSwaggerGen 方法的定义，我们需要添加 SwaggerGenOptions 到方法中

```csharp
public static IServiceCollection AddSwaggerGen(
    this IServiceCollection services,
    Action<SwaggerGenOptions> setupAction = null)
```

接下来，以不同版本的 API 接口为例。创建一个枚举类来标示版本

```csharp
public enum APIVersionEnum
{
    Version_0_0 = 1,
    Version_1_0 = 2, 
}
```

在 SwaggerGenOptions 中，需要根据不同的枚举值做出不同的操作

```csharp
services.AddSwaggerGen(options =>
{
	typeof(APIVersionEnum).GetEnumNames().ToList().ForEach(version =>
	{  
		// 处理逻辑
	});
});
```

额外地，需要在项目生成属性中勾选输出 API 文档，并在程序中反射到该文档

```csharp
public static void InitSwagger(this IServiceCollection services)
{
    services.AddSwaggerGen(options =>
    {
        typeof(APIVersionEnum).GetEnumNames().ToList().ForEach(version =>
        {
            options.SwaggerDoc(version, version switch
            {
                "Version_0_0" => new OpenApiInfo()
                {
                    Title = "Version_0_0",
                    Version = "v0.0",
                    Description = "description",
                    Contact = new OpenApiContact()
                    {
                        Name = "author",
                        Url = new Uri("http://gz4nna.github.io")
                    }
                },
                "Version_1_0" => new OpenApiInfo()
                {
                    Title = "Version_1_0",
                    Version = "v1.0",
                    Description = "description",
                    Contact = new OpenApiContact()
                    {
                        Name = "author",
                        Url = new Uri("http://gz4nna.github.io")
                    }
                },
                _ => new OpenApiInfo()
                {
                    Title = "title",
                    Version = "v0.0",
                    Description = "description",
                    Contact = new OpenApiContact()
                    {
                        Name = "author",
                        Url = new Uri("http://gz4nna.github.io")
                    }
                }
            });                  
        });

        var xmlFileName = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        options.IncludeXmlComments(Path.Combine(AppContext.BaseDirectory, xmlFileName), true);
    });
}
```

以上为对服务的修改，SwaggerDoc 定义如下

```csharp
public static void SwaggerDoc(this SwaggerGenOptions swaggerGenOptions, string name, OpenApiInfo info)
{
    swaggerGenOptions.SwaggerGeneratorOptions.SwaggerDocs.Add(name, info);
}
```

我们用 APIVersionEnum 中的枚举值将原本一整个 Swagger 文档分开，每部分展示的内容除了对 API 本身的手动划分外，还与指定的 OpenApiInfo 有关，常用的属性诸如 `Title`, `Description`, `Version`, `TermsOfService`, `Contact`, `License` 等等

接下来在路由中添加终结点

```csharp
public static void InitSwagger(this WebApplication app)
{
    app.UseSwagger();
    app.UseSwaggerUI(options =>
    {
        typeof(APIVersionEnum).GetEnumNames().ToList().ForEach(version =>
        {
            options.SwaggerEndpoint($"/swagger/{version}/swagger.json", $"{version}");
        });
    });
}
```

最后将两处变更同步到 Program.cs 中，原本的代码变成了

```csharp
builder.Services.InitSwagger();

...

if (app.Environment.IsDevelopment())
{
    app.InitSwagger();
}
```

现在对于一个新的 Controller，可以指定所属版本并在文档中体现出来

```csharp
[ApiController]
[Route("api/[controller]/[action]")]
[ApiExplorerSettings(GroupName = nameof(APIVersionEnum.Version_0_0))]
```

