
# .NET 单文件执行程序拆解器 SingleFileExtractor


.NET 现在支持将程序打包为单文件格式，这方便了部署，问题是，我们不能直接看到程序中使用了哪些 DLL，更不能简单地通过查看文件属性的方式，看到这些 DLL 的版本。要是可以像使用 Zip 一样，可以打开这个合成的文件，直接查看内容就好了。


SingleFileExtractor 就是这样的工具。


它可以从单文件的应用程序中将其中包含的内容，包括程序集、配置文件等等，拆解到文件夹中，方便开发人员进行分析。


## 1\. 安装


它的安装也很方便，通过 dotnet 工具的方式进行安装和使用。



```
dotnet tool install -g sfextract

```


> 注意，当前 2024/12/20 使用 `dotnet tool install -g sfextract` 安装此工具，需要 .NET 6\.0


## 2\. 使用


### 2\.1 命令行



```
sfextract [file] -o|--output [directory]

```

例如



```
sfextract Application.exe -o path/to/output/

```

### 2\.2 列出其中的文件



```
sfextract Application.exe

```

## 3\. 在程序中使用


在项目中安装 [SingleFileExtractor.Core](https://github.com) NuGet 包，


然后，使用 SingleFileExtractor 提供的 ExecutableReader 类来打开文件。



```
var reader = new ExecutableReader("application.exe");

```

### 3\.1 读取启动信息


当你希望知道程序的入口点的时候，可以这样读取启动信息



```
var reader = new ExecutableReader("application.exe");
var startupInfo = reader.StartupInfo;

```

### 3\.2 提取全部文件。



```
var reader = new ExecutableReader("application.exe");

// Validate if executable is a single file executable, and can be extracted
var isSingleFile = reader.IsSingleFile;

if (isSingleFile)
{
    // Extract specific file entry
    await reader.Manifest.Entries[0].ExtractToFileAsync("example.dll");
    // , or create an in-memory stream of a specifc file entry
    var stream = await reader.Manifest.Entries[0].AsStreamAsync()
    
    // Extract all files to a directory
    await reader.ExtractToDirectoryAsync("path/to/output");
}

```

### 3\.3 提取指定内容到文件



```
var reader = new ExecutableReader("application.exe");
await reader.Manifest.Entries[0].ExtractToFileAsync("example.dll");

```

### 3\.4 提取指定内容到流



```
var reader = new ExecutableReader("application.exe");
var stream = await reader.Manifest.Entries[0].AsStreamAsync()

```

## 4\. 为什么创造它？


我正在处理的另一个程序需要我从单文件的执行程序中提取内容，另外我还看到有人在询问这个问题，所以，我决定将它开发为一个 dotnet 工具的形式，并使用 NuGet 包的形式发布。


项目地址：[https://github.com/Droppers/SingleFileExtractor](https://github.com):[蓝猫加速器配置下载](https://yunbeijia.com)


