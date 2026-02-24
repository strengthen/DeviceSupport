# [English](./README.md) | 简体中文

# 一、运行Xcode时出现：
### This operation can fail if the version of the OS on the device is incompatible with the installed version of Xcode. You may also need to restart your mac and device in order to correctly detect compatibility.
### This xxx is running iOS xxx, which may not be supported by this version of Xcode.
## 1、点击下载指定的iOS系统支持文件：https://github.com/strengthen/DeviceSupport
## 2、下载上面库中列出的您需要的版本，解压版本文件为文件夹。
## 3、打开【访达】，输入快捷键：【Command + Shift + G】，输入文件夹位置：/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport ，其中【Xcode.app】为Xcode名称，如果下载多个Xcode，则输入对应Xcode的名称，将解压的的文件夹放入DeviceSupport文件夹。
## 4、重新启动Xcode。

# 二、Xcode 15在某些 Cocoa pod 中存在构建问题，因为其 XcodeDefaults 工具链内容中缺少“.a”文件。以下是 Xcode 15 中所有缺失的文件。
## 您可以下载并将其粘贴到此路径中：
## /Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/arc/
## 注意：如果 lib 文件夹不存在，请创建一个名为“arc”的文件夹。

# 三、欢迎点击进入我的App Store主页下载本人已上架发布的应用。
<a href='https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397'><img height='70' alt='Download from AppStore' src='https://img.whalenas.com:283/image/202207141215375.png' /></a>
> [https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397](https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397)