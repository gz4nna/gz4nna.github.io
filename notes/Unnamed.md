# 如何封装一个 NuGet 包

创建一个类库,包含接口和实现,将需要的功能在方法中暴露,将使用的工具隐藏在方法的实现中
在 .csproj 文件中添加 NuGet 包的元数据

```xml
<PropertyGroup>
	<!-- 编译目标框架 -->
	<TargetFramework>net6.0</TargetFramework>
	
	<!-- 包唯一标识符 -->
	<PackageId>MyCustomLibrary</PackageId>
	
	<!-- 包版本 -->
	<Version>1.0.0</Version>
	
	<!-- 包作者 -->
	<Authors>YourNameOrCompany</Authors>
	
	<!-- 包描述 -->
	<Description>This is a library.</Description>
	
	<!-- 包版权信息 -->
	<Copyright>Copyright © 2024 YourCompany</Copyright>
	
	<!-- 包标签（关键词） -->
	<PackageTags>EFCore;Redis;Caching;Repository</PackageTags>
	
	<!-- 许可证表达式或自定义许可证文件 -->
	<PackageLicenseExpression>MIT</PackageLicenseExpression>	
	<!-- <PackageLicenseFile>LICENSE.txt</PackageLicenseFile> -->
	<!-- 可选，使用自定义文件代替许可证表达式 -->
	
	<!-- 代码仓库 URL -->
	<RepositoryUrl>https://url</RepositoryUrl>
	
	<!-- 包图标（需添加到项目并设置“在输出目录中复制”） -->
	<Icon>icon.png</Icon>
	
	<!-- 发布日志 -->
	<ReleaseNotes>Basic repository and caching support.</ReleaseNotes>
	
	<!-- 项目主页或文档 URL -->
	<ProjectUrl>https://yourprojecthomepage.com</ProjectUrl>
	
	<!-- 是否要求用户在安装时接受许可证 -->
	<RequireLicenseAcceptance>true</RequireLicenseAcceptance>
	
	<!-- 简短描述 -->
	<Summary>A lightweight library for caching and data access.</Summary>
	
	<!-- 主要编程语言 -->
	<Language>en-US</Language>
	
	<!-- 包显示名称 -->
	<Title>My Custom Library</Title>
	
	<!-- 构建时生成 NuGet 包 -->
	<GeneratePackageOnBuild>true</GeneratePackageOnBuild>
</PropertyGroup>
```

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

# 使用 Redis

