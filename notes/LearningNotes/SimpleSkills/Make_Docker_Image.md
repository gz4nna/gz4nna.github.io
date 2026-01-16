# 如何将项目做成 Docker 镜像

在项目目录下制作 Dockerfile 文件,注意检查使用的端口

```dockerfile
# 使用官方 .NET 8 SDK 镜像来构建应用程序
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# 使用官方 .NET 8 SDK 镜像来进行构建
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# 将项目文件复制到 Docker 容器中
COPY ["YourProjectName.csproj", "./"]

# 还原依赖项
RUN dotnet restore "./YourProjectName.csproj"

# 将项目的所有文件复制到容器中并进行构建
COPY . .
RUN dotnet publish "./YourProjectName.csproj" -c Release -o /app/publish

# 创建最终镜像并从生成的发布输出运行应用程序
FROM base AS final
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "YourProjectName.dll"]

```

曾经碰到情况为引用了类库,但是不在同一目录下,解决方法

1. 放弃使用类库,转为在该项目中实现
2. 在父级目录下创建 Dockerfile,拷贝使用的所有目录,并使用 .sln 文件来还原依赖

```dockerfile
# 使用官方 .NET 8 SDK 镜像来构建应用程序
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# 将整个解决方案文件夹复制到容器中
COPY . .

# 还原整个解决方案的依赖项，以便处理所有项目的引用
RUN dotnet restore "SolutionFile.sln"

# 使用解决方案文件来构建项目并发布
RUN dotnet publish "MainProject/MainProject.csproj" -c Release -o /app/publish

# 使用运行时镜像来执行已发布的应用
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "MainProject.dll"]
```

在当前目录下运行 cmd 命令

```shell
docker build -t yourimagename .
```

最后创建容器,注意检查端口是否冲突

```shell
docker run -d -p 8080:80 --name yourcontainername yourimagename
```

