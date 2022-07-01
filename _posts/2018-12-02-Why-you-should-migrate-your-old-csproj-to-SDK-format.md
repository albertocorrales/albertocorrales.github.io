---
layout: post
title: Why you should migrate your old csproj to SDK format
date: 2018-12-02 21:27:03 UTC
updated: 2018-12-02 21:27:03 UTC
comments: false
categories: 2017 csproj NuGet tips & tricks Visual Studio
---

## Introduction 

With the introduction of Visual Studio 2017, the format used for csproj was updated including several advantages. This new format will be used when you create a new .Net Core project or .Net Standard library. In spite of the fact that VS2017 continues supporting old csproj format, you can migrate your old projects to SDK format. As a result, you will have the following benefits:

- **Cleaner csproj files.** With new format, a lot of things can be omitted, so your csproj files will be cleaner.
- **Files are included using a wildcard**, instead of including every single file.
- Solution project **references are simplier**.
- **NuGet reference packages as [PackageReferences](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files)**, so you won't need packages.config and you will be able to use [wildcard in your references](https://docs.microsoft.com/en-us/nuget/reference/package-versioning#version-ranges-and-wildcards) (for example, 1.0.\*).
- **NuGet package definition as a part of the project file.** You won't need more nuspec files.
- **Definition of AssemblyInfo attributes in csproj**, you won't need more AssemblyInfo.cs files. In addition, you will be able to use features such as **Directory.Build.props** to centralize this info. Go to this link for more info: [https://www.albertocorrales.com/2018/11/maintaining-assemblyinfo-for-multiple.html](https://www.albertocorrales.com/2018/11/maintaining-assemblyinfo-for-multiple.html)
- **Your project will be ready to use .Net Standard 2** as a target if you want to do it in the future.

## Migrate your csproj to SDK format

### 1) Migrating from Visual Studio (only PackageReferences)

From Visual Studio, there is not an automatic way to convert your projects to SDK format. The only thing VS allow you to do form IDE is converting NuGet references to PackageReference format. To do it, just select "references" or "packages.config" and click on "Migration packages.config to PackageReferences".

[![](https://3.bp.blogspot.com/-qQ9o2-bpsgY/XARLREXj6UI/AAAAAAAAFEo/0Xvc9nmR21sG630RASORsMpD8NUonyPjgCLcBGAs/s400/1.png)](https://3.bp.blogspot.com/-qQ9o2-bpsgY/XARLREXj6UI/AAAAAAAAFEo/0Xvc9nmR21sG630RASORsMpD8NUonyPjgCLcBGAs/s1600/1.png)

In order to migrate ASP.NET projects, you won't see the previous menu, so you will have to do the [following workaround](https://github.com/NuGet/docs.microsoft.com-nuget/issues/860#issuecomment-409207305):

- Open the csproj
- Replace the ProjectGuid by _<ProjectGuid>{7C796B6B-86B5-4C57-ADAA-12CF1FECDA71}</ProjectGuid>_ and remove _ProjectTypeGuids_
- Now right click on reference, you should be able to migrate
- Put back the previous ProjectType and ProjectTypeGuids

### 2) SDK format manual migration

In order to convert your csproj, the default method is manual conversion. To do it, the easiest way is starting your csproj from the scratch. To know all the things you can remove, you can follow this tutorial, which explains the conversion process: [https://natemcmaster.com/blog/2017/03/09/vs2015-to-vs2017-upgrade/](https://natemcmaster.com/blog/2017/03/09/vs2015-to-vs2017-upgrade/)

### 3) SDK format migration using Tool CsprojToVs2017

Manual mode can be useful if you have few project, but it is difficult to scale if you have a lot of projects to convert. Luckily, there is a GitHub project named CsprojToVs2017, which can be very handy to convert multiple projects automaticly: [https://github.com/hvanbakel/CsprojToVs2017](https://github.com/hvanbakel/CsprojToVs2017)

This tool is very helpful, but before migration, you will see the resulting conversion it not as clearer as it should, you can finish the migration manually by removing unnecessary parts in csproj.

## Migration Example

### Old csproj

Here we have an example of a migration for a project. This is the old version:

    <?xml version="1.0" encoding="utf-8"?><Project ToolsVersion="15.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">  <Import Project="$(MSBuildExtensionsPath)\$(MSBuildToolsVersion)\Microsoft.Common.props" Condition="Exists('$(MSBuildExtensionsPath)\$(MSBuildToolsVersion)\Microsoft.Common.props')" />  <PropertyGroup>    <Configuration Condition=" '$(Configuration)' == '' ">Debug</Configuration>    <Platform Condition=" '$(Platform)' == '' ">AnyCPU</Platform>    <ProjectGuid>{214F3A00-13FC-43BF-AFEF-A740C8B5461E}</ProjectGuid>    <OutputType>Library</OutputType>    <AppDesignerFolder>Properties</AppDesignerFolder>    <RootNamespace>LibraryExample</RootNamespace>    <AssemblyName>LibraryExample</AssemblyName>    <TargetFrameworkVersion>v4.7</TargetFrameworkVersion>    <FileAlignment>512</FileAlignment>    <Deterministic>true</Deterministic>  </PropertyGroup>  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' ">    <DebugSymbols>true</DebugSymbols>    <DebugType>full</DebugType>    <Optimize>false</Optimize>    <OutputPath>bin\Debug\</OutputPath>    <DefineConstants>DEBUG;TRACE</DefineConstants>    <ErrorReport>prompt</ErrorReport>    <WarningLevel>4</WarningLevel>  </PropertyGroup>  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|AnyCPU' ">    <DebugType>pdbonly</DebugType>    <Optimize>true</Optimize>    <OutputPath>bin\Release\</OutputPath>    <DefineConstants>TRACE</DefineConstants>    <ErrorReport>prompt</ErrorReport>    <WarningLevel>4</WarningLevel>  </PropertyGroup>  <ItemGroup>    <Reference Include="CommonServiceLocator, Version=2.0.4.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\CommonServiceLocator.dll</HintPath>    </Reference>    <Reference Include="Newtonsoft.Json, Version=11.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed, processorArchitecture=MSIL">      <HintPath>..\packages\Newtonsoft.Json.11.0.2\lib\net45\Newtonsoft.Json.dll</HintPath>    </Reference>    <Reference Include="System" />    <Reference Include="System.Configuration" />    <Reference Include="System.Configuration.ConfigurationManager, Version=4.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51, processorArchitecture=MSIL">      <HintPath>..\packages\System.Configuration.ConfigurationManager.4.4.1\lib\net461\System.Configuration.ConfigurationManager.dll</HintPath>    </Reference>    <Reference Include="System.Core" />    <Reference Include="System.Xml.Linq" />    <Reference Include="System.Data.DataSetExtensions" />    <Reference Include="Microsoft.CSharp" />    <Reference Include="System.Data" />    <Reference Include="System.Net.Http" />    <Reference Include="System.Xml" />    <Reference Include="Unity.Abstractions, Version=3.3.1.0, Culture=neutral, PublicKeyToken=6d32ff45e0ccc69f, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.Abstractions.dll</HintPath>    </Reference>    <Reference Include="Unity.Configuration, Version=5.2.5.0, Culture=neutral, PublicKeyToken=6d32ff45e0ccc69f, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.Configuration.dll</HintPath>    </Reference>    <Reference Include="Unity.Container, Version=5.8.11.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.Container.dll</HintPath>    </Reference>    <Reference Include="Unity.Interception, Version=5.5.5.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.Interception.dll</HintPath>    </Reference>    <Reference Include="Unity.Interception.Configuration, Version=5.1.7.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.Interception.Configuration.dll</HintPath>    </Reference>    <Reference Include="Unity.RegistrationByConvention, Version=2.1.9.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.RegistrationByConvention.dll</HintPath>    </Reference>    <Reference Include="Unity.ServiceLocation, Version=2.1.2.0, Culture=neutral, PublicKeyToken=489b6accfaf20ef0, processorArchitecture=MSIL">      <HintPath>..\packages\Unity.5.8.11\lib\net47\Unity.ServiceLocation.dll</HintPath>    </Reference>  </ItemGroup>  <ItemGroup>    <Compile Include="Class1.cs" />    <Compile Include="Properties\AssemblyInfo.cs" />  </ItemGroup>  <ItemGroup>    <ProjectReference Include="..\LibraryExample2\LibraryExample2.csproj">      <Project>{a984ea74-aa5d-44ca-a404-2ed068417af9}</Project>      <Name>LibraryExample2</Name>    </ProjectReference>  </ItemGroup>  <ItemGroup>    <None Include="packages.config" />  </ItemGroup>  <Import Project="$(MSBuildToolsPath)\Microsoft.CSharp.targets" /></Project>

### New csproj

To migrate this project I used CsprojToVs2017 tool and then I removed unnecessary parts manually. Notice migrated csproj included nuspec and AssemblyInfo information, so those files were removed.

    <Project Sdk="Microsoft.NET.Sdk">  <PropertyGroup>    <ProjectGuid>{214F3A00-13FC-43BF-AFEF-A740C8B5461E}</ProjectGuid>    <TargetFramework>net47</TargetFramework>    <AssemblyTitle>LibraryExample</AssemblyTitle>    <Product>LibraryExample</Product>    <Copyright>Copyright ©  2018</Copyright>  </PropertyGroup>  <ItemGroup>    <PackageReference Include="Newtonsoft.Json" Version="11.0.2" />    <PackageReference Include="System.Configuration.ConfigurationManager" Version="4.4.1" />    <PackageReference Include="System.Reflection.Emit" Version="4.3.0" />    <PackageReference Include="Unity" Version="5.8.11" />  </ItemGroup>  <ItemGroup>    <Reference Include="System.Configuration" />    <Reference Include="System.Data.DataSetExtensions" />    <Reference Include="Microsoft.CSharp" />    <Reference Include="System.Net.Http" />  </ItemGroup>  <ItemGroup>    <ProjectReference Include="..\LibraryExample2\LibraryExample2.csproj" />  </ItemGroup></Project>

One think I like is that after migration, you will see the references in Visual studio organized into 3 categories: Assemblies, NuGet and Projects. As a consequence. you don't need to find out the source of a reference exploring the csproj content.

[![](https://3.bp.blogspot.com/-GIKdpzLvAT8/XARLc-rJLzI/AAAAAAAAFEs/qifv7Bkadmgajrv_AWIm5j71a5SWX5bYACLcBGAs/s400/2.PNG)](https://3.bp.blogspot.com/-GIKdpzLvAT8/XARLc-rJLzI/AAAAAAAAFEs/qifv7Bkadmgajrv_AWIm5j71a5SWX5bYACLcBGAs/s1600/2.PNG)

I hope you found this article useful,

Happy coding!!
