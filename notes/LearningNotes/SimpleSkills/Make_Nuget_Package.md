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
