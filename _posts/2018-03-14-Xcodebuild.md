---
layout:     post
title:      "Xcodebuild从入门到精通"
subtitle:   "深入理解Xcode命令行工具"
date:       2018-03-14 12:00:00
author:     "Marco Liu"
header-img: "img/post-bg-2015.jpg"
catalog:    true
keywords: "Xcodebuild,iOS开发,Xcode，Xcode命令行工具，命令行工具"
description: "深入理解Xcode命令行工具"
tags:
    - CI系统
---

> “Better late than never. ”


## 前言

###### 命令行工具包简介

Command Line Tools（命令行工具包）是一个轻小的、可以与Xcode分开下载的、允许你在OS X上进行命令行开发的工具包。它由两部分组成：OS X SDK和类似Clang等安装在/usr/bin下的命令行工具。例如gcc/g++编译器、make、git、nasm、xcodebuild、xcrun等等。在从App Store上下载Xcode后，默认是不会安装command Line Tools的。

###### 安装命令行工具包

打开终端，执行`xcode-select --install`，然后会弹出一个窗口，点击install，接受协议，等待安装完成，Command Line tools会安装在/Library/Developer路径下面。

###### xcode-select指令

OS X自带xcode-select，这是一个安装于/usr/bin中的命令行工具。顾名思义，如果你的电脑上有多个不同版本的Xcode，它可以用来切换Xcode的默认的版本。而上面的`xcode-select --install`则用来安装命令行工具包。

###### man指令

man指令加上要查询的说明主题来阅读线上手册（manual page），通常主题的名称就是指令或是函数的名称,例如`man xcodebuild` ,可以用来查看xcodebuild指令线上的使用手册。

###### xcpretty

xcpretty是一个用来格式化（美化）xcodebuild命令行工具输出日志的小工具，安装命令:
`gem install xcpretty `，使用方法：`xcodebuild | xcpretty`

---
## 正文

先奉上xcodebuild的[官方文档](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html)，或者在终端中执行`man xcodebuild`来查看xcodebuild的使用手册。

### xcodebuild的使用

`xcodebuild`指令必须在包含`projectname .xcodeproj`的目录下才能使用，它的作用是构建Xcode项目或工作区。如果要build的是Xcode工作区，则必须同时传递`-workspace`和`-scheme`选项来定义build。如果目录下有多个project或target，则`-project`和``-scheme``选项的默认参数为第一个project或scheme。

`xcodebuild`指令默认的configuration是在Xcode工程里的project/info里设置，如`下图`所示：
![](/img/in-post/xcode/xcode1.jpg)

---
##### xcodebuild使用例子


```swift
单元测试

1、xcodebuild test -project PROJECT_NAME.xcodeproj -scheme SCHEME_NAME 
-destination 'platform=iOS Simulator,name=iPhone 6s,OS=11.2' -
configuration Debug -derivedDataPath output

//在iOS11.2的模拟器iPhone6s上对scheme为UnitTestTests执行单元测试,并把生成的缓存文件存储在./output里
```

```swift
archive操作

2、xcodebuild archive  -workspace PROJECT_NAME.xcworkspace   
-scheme SCHEME_NAME -configuration release -archivePath  
EXPORT_ARCHIVE_PATH 

//以release模式归档项目到EXPORT_ARCHIVE_PATH路径
```

```swift
导出IPA文件

3、xcodebuild -exportArchive -archivePath EXPORT_ARCHIVE_PATH    
-exportPath EXPORT_IPA_PATH -exportOptionsPlist ExportOptionsPlistPath
-allowProvisioningUpdates

//用exportOptionsPlist文件里的导出配置信息来导出放在EXPORT_ARCHIVE_PATH路径下  的xcarchive文件，导出IPA文件到EXPORT_IPA_PATH路径下。并且允许自动更新Provision文件
```

```swift
指定测试单个用例方法 

4、xcodebuild test -project PROJECT_NAME.xcodeproj -scheme SCHEME_NAME 
-destination 'platform=iOS Simulator,name=iPhone 6s,OS=11.2' -only-testing:SCHEME_NAME/CLASS_NAME/FUNC_NAME 

```

---
##### action介绍

`test`操作需要指定设备标识符 ：`-destination`。其他操作默认使用的是`Generic iOS Device`。

| 操作 | 介绍 |
| ------| ------ |
| `build` |  在根目录执行build操作，也是默认的操作。 |
| `build-for-testing`  | 在根目录执行build操作,要求指定一个scheme，然后会在derivedDataPath/Build/Products目录下生成一个`.xctestrun`文件，这个文件包含执行测试的必要信息。对于每个测试目标，它都包含测试主机路径的条目，一些环境变量，命令行参数等待 |
| `analyze`  | 在根目录执行analyze操作，要求指定一个scheme |
| `archive`  |  在根目录执行archive操作，要求指定一个scheme |
| `test`  |   在根目录执行test操作，要求指定一个scheme |
| `test-without-building`  | 在已经编译好的bundle上执行test操作，如果提供了 `-scheme`选项，则在derivedDataPath下寻找bundle，如果提供了`-xctestrun`选项，则在给定的路径下寻找bundle。 |
| `clean`  | 在根目录执行clean操作。在debug（release）下，clean操作其实就是做了删除`derivedDataPath/Debug-iphoneos(Release-iphoneos)`下的`.app`文件和`.dSYM`文件还有`derivedDataPath/.build下的文件夹`的操作。 |
| `install`  |  build项目，会在.dst目录下生成一个.app文件,例如这路径。`/tmp/UnitTest.dst/Applications/UnitTest.app` |

---
##### 选项介绍
<br>
`-destination`代表的是设备的描述。例如：`-destination 'platform=iOS Simulator,name=iPhone 6s,OS=11.2'`。 其中`platform`下有`iOS Simulator`和`iOS`等等。

1、`iOS Simulator`说明选择的是iOS模拟器，需要提供`name`、`OS`或者`id`。<br>
2、`iOS`说明选择的是真实设备，需要提供`name`或者`id`。

`name`、`OS`和`id` 可以从下图获得：
![](/img/in-post/xcode/xcode2.jpg)

---
如果想在命令行中修改项目的buildsetting，只在`xcodebbuild`命令后面加上`buildsetting=value`。例如`arch = arm64`，说明可以将arch设置为arm64。可以通过`xcodebuild -showBuildSettings`查看当前项目的buildsetting。


| 选项 | 参数 | 介绍 |
| ------| ------ | ------ |
| `-project` | projectname | 指定projectname |
| ``-target``  | targetname | 指定targetname |
| `-alltargets`  | 无 | 指定项目中的所有target |
| `-workspace`  | workspacename | 指定workspacename |
| `-scheme`  | schemename | 指定scheme |
| `-destination`  | destinationspecifier | 通过destination描述来指定设备,例如`'platform=iOS Simulator,name=iPhone 6s,OS=11.2'` |
| `-destination-timeout`  | timeout | 指定搜索目标设备的超时时间，默认值是30秒 |
| `-configuration`  | configurationname | 指定构建方式，`Debug`或者`Release` |
| `-arch`  | architecture | 指定architecture |
| `-sdk`  | sdkname | 指定sdk,例如`iphoneos11.2` |
| `-showsdks`  | 无 | 列出Xcode知道的所有可用SDK，包括适合使用的规范名称与-sdk。不启动构建。 |
| `-list `  | 无 | 列出项目中的目标和配置，或工作区中的方案。不启动构建。 |
| `-derivedDataPath`  | 文件路径 | 指定生产DerivedData的文件路径 |
| `-resultBundlePath`  | 文件路径 | 指定生产result的文件路径，其中会包含一个info.plist |
| `-showBuildSettings`  | 无 | 列出target的`Build settings` |
| `-enableAddressSanitizer`  | `YES`或 `NO` | 项目scheme里Diagnostics下的选项，暂未研究具体能做什么。 |
| `-enableThreadSanitizer`  | `YES`或 `NO` | 项目scheme里Diagnostics下的选项，暂未研究具体能做什么。 |
| `-enableCodeCoverage`  | `YES`或 `NO` | 项目scheme里test里info下的选项，可以控制是否生成代码覆盖率 |
| `-testLanguage`  | language |使用`ISO 639-1`语种名称来指定test时的APP语言 |
| `-testRegion`  | region | 使用`ISO 3166-1`地区名称来指定test时的APP地区 |
| `-allowProvisioningUpdates`  | 无 | 允许xcodebuild与Apple Developer网站进行通信。 对于自动签名的目标，xcodebuild将创建并更新配置文件，应用程序ID和证书。 对于手动签名的目标，xcodebuild将下载缺失或更新的供应配置文件， 需要在Xcode的帐户首选项窗格中添加开发者帐户。 |
| `-exportArchive`  | 无 | 导出`IPA文件`。 需要-archivePath，-exportPath和 -exportOptionsPlist一起使用。 不能与action一起使用。 |
| `-archivePath`  | xcarchive路径 | 指定archive操作生成归档的路径。或者在使用`-exportArchive`时指定归档的路径。 |
| `-exportPath`  | destination路径 | 指定导出IPA文件到哪个路径，其中在最后要包括IPA文件的名称。 |
| `-exportOptionsPlist`  | file路径 | 导出IPA文件时，需要指定一个ExportOptions.plist文件，如果不知道怎么填写这个文件，可以先用Xcode手动打包一次，导出文件中会有ExportOptions.plist，然后手动copy就好。 |
| `-exportLocalizations`  | 无 | 将本地化导出到XLIFF文件。 需要-project和-localizationPath。 不能与action一起使用。 |
| `-importLocalizations`  | 无 | 从XLIFF文件导入本地化。 需要-project和-localizationPath。 不能与action一起使用。 |
| `-localizationPath`  | path | 指定目录或单个XLIFF本地化文件的路径。 |
| `-exportLanguage`  | language | 指定包含在本地化导出中的可选`ISO 639-1`语言，可以重复指定多种语言，可能被排除以指定导出仅包含开发语言字符串。 |
| `-xcconfig`  | .xcconfig文件的路径 | 构建target时使用自定义的设置。这些设置将覆盖所有其他设置，包括在命令行上的设置。 |
| `-xctestrun`  | .xctestrun文件的路径 | 指定.xctestrun文件的路径，只能在test-without-building操作中存在 |
| `-skip-testing`  | `TestClass`或`TestMethod` | 跳过指定的测试单元，然后test剩下的测试单元，测试单元可以是一个测试类或者测试方法|
| `-only-testing`  | `TestClass`或`TestMethod` | 只test指定的测试单元，`-only-testing`优先于`-skip-testing` |
| `-disable-concurrent-testing`  | 无|限制并发测试，只能在指定的设备上串行测试 |
| `-dry-run, -n`  | 无 | 打印将执行的命令，但不执行它们。 |
| `-skipUnavailableActions`  | 无 | 跳过无法执行的操作而不是失败的操作。 这个选项是只有在scheme通过的情况下才会被使用。 |
| `-toolchain`  | identifier或name | 使用identifier或name指定的toolchain |
| `-quiet`  | 无 | 除了警告和错误外，不打印任何输出。 |
| `-verbose`  | 无 | 会打印额外的一些状态信息。 |
| `-version`  | 无 | 显示xcode版本信息。不会触发构建。当和-sdk一起使用时，将会显示SDK的版本或者所有的SKDs |
| `-license`  | 无 | 显示Xcode和SDK许可协议。 允许接受许可协议，而无需启动Xcode本身。|
| `-checkFirstLaunchStatus`  | 无 | 检查是否需要执行首次启动任务。 |
| `-runFirstLaunch`  | 无 | 安装软件包并同意许可证。 |
| `-usage`  | 无 | 打印Xcode的usage信息 |


| `-allowProvisioningDeviceRegistration`  | 无  | 如有必要，允许xcodebuild在Apple Developer网站上注册您的目标设备。需要-allowProvisioningUpdates。 |

| `-enableUndefinedBehaviorSanitizer`  | `YES`或 `NO` | 项目scheme里Diagnostics下的选项，暂未研究具体能做什么。 |

| `-maximum-concurrent-test-device-destinations`| number | 限制最多多少台真实设备并发测试 |

| `-maximum-concurrent-test-simulator-destinations`| number | 限制最多多少台并发测试 |


