# [English](./README.md) | 简体中文

# 一、欢迎点击进入我的App Store主页下载本人已上架发布的应用。
<a href='https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397'><img height='70' alt='Download from AppStore' src='https://img.whalenas.com:283/image/202207141215375.png' /></a>
> [https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397](https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397)
# 关注Telegram频道，获取最新信息。
> [https://t.me/iTelecast](https://t.me/iTelecast)

# 二、运行Xcode时出现：
### This operation can fail if the version of the OS on the device is incompatible with the installed version of Xcode. You may also need to restart your mac and device in order to correctly detect compatibility.
### This xxx is running iOS xxx, which may not be supported by this version of Xcode.
## 1、点击下载指定的iOS系统支持文件：https://github.com/strengthen/DeviceSupport
## 2、下载上面库中列出的您需要的版本，解压版本文件为文件夹。
## 3、打开【访达】，输入快捷键：【Command + Shift + G】，输入文件夹位置：/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport ，其中【Xcode.app】为Xcode名称，如果下载多个Xcode，则输入对应Xcode的名称，将解压的的文件夹放入DeviceSupport文件夹。
## 4、重新启动Xcode。

# 三、Xcode 15在某些 Cocoa pod 中存在构建问题，因为其 XcodeDefaults 工具链内容中缺少“.a”文件。以下是 Xcode 15 中所有缺失的文件。
## 您可以下载并将其粘贴到此路径中：
## /Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/arc/
## 注意：如果 lib 文件夹不存在，请创建一个名为“arc”的文件夹。

# 四、符号文件
## 4.1、Symbols
Symbols folders in ~/Library/Developer/Xcode/iOS DeviceSupport
## 4.2、从固件中获取符号文件
iOS固件会有系统符号文件，通过一些工具，可以从固件中提取出符号文件，
### (1) 下载对应版本的固件，ipsw文件较大，可以选择网络空闲时间来下载。固件下载网址：
* [https://ipsw.me/product/iPhone](https://ipsw.me/product/iPhone)
* [https://www.i4.cn/firmware_iPhone_iPhone%20X_____.html](https://www.i4.cn/firmware_iPhone_iPhone%20X_____.html)
* [https://iosgujian.com/iPhone/iPhone11/iPhone12,1](https://iosgujian.com/iPhone/iPhone11/iPhone12,1)
### (2) 确定符号文件的位置
将ipsw文件解压，它实际是个zip文件，因此换下后缀名可以解压出来，得到下面的一些文件和文件夹
```shell
$ iPhone14,5_15.0.1_19A348_Restore.ipsw ls -l
total 12722096
-rw-r--r--@  1 wesley_chen  staff  6251791817 Jan  9  2007 018-79898-003.dmg
-rw-r--r--@  1 wesley_chen  staff   121403931 Jan  9  2007 038-42528-641.dmg
-rw-r--r--@  1 wesley_chen  staff   123465243 Jan  9  2007 038-42637-643.dmg
-r--r--r--@  1 wesley_chen  staff      314550 Jan  9  2007 BuildManifest.plist
drwxr-xr-x@ 33 wesley_chen  staff        1056 Jan  9  2007 Firmware
-r--r--r--@  1 wesley_chen  staff        1030 Jan  9  2007 Restore.plist
-rw-r--r--@  1 wesley_chen  staff    16727656 Jan  9  2007 kernelcache.release.iphone14
```
按照文件排序，找到最大的那个dmg文件，这里是018-79898-003.dmg，将它打开。
根据下面的路径，找到dyld_shared_cache_arm64e文件
```shell
/System/Library/Caches/com.apple.dyld
```
还有其他两个文件
* /System/Library/Caches/com.apple.dyld/dyld_shared_cache_arm64e.1
* /System/Library/Caches/com.apple.dyld/dyld_shared_cache_arm64e.symbols
### (3) 获取dsc_extractor工具
有很多工具可以从dyld_shared_cache_arm64e文件提取符号文件，但是dsc_extractor提取的产物，是和Xcode连接真机提取的产物是一样的。
这篇文章的描述[^1]，如下：
> [dsc_extractor (source code)](https://opensource.apple.com/source/dyld/). More info [here](https://gist.github.com/NSExceptional/85527151eeec4b0640187a0a165da1cd). It produces the best results among all tools, but without branch islands workaround. Do note this is the exact same output provided by XCode automatically.
所以使用dsc_extractor工具，是最佳选择。但是dsc_extractor工具不是现成的，需要自己编译出来。
有两个文章[^2][^3]介绍如何编译这个工具，按照它们的步骤一步步来
#### （3.1）. 下载dyld文件
Apple的opensource网站提供下载dyld文件，地址是：https://opensource.apple.com/tarballs/dyld/
找到最新的dyld文件的下载地址。
执行下面几个命令
```shell
$ wget https://opensource.apple.com/tarballs/dyld/dyld-852.2.tar.gz
$ tar -xvzf dyld-852.2.tar.gz
```
dyld文件，由于更新了很多版本，因此解压后的文件结构可能会不一样。
#### （3.2）. 从dsc_extractor.cpp获取extract代码
找到需要的dsc_extractor.cpp，位置如下
```
dyld-852.2/dyld3/shared-cache/dsc_extractor.cpp
```
说明
> 如果有dyld.xcodeproj，可以打开搜一下dsc_extractor.cpp
在dsc_extractor.cpp的代码中，找到下面一段被if条件编译注释掉的代码，如下
```c
#if 0
// test program
#include <stdio.h>
#include <stddef.h>
#include <dlfcn.h>

typedef int (*extractor_proc)(const char* shared_cache_file_path, const char* extraction_root_path,void (^progress)(unsigned current, unsigned total));

int main(int argc, const char* argv[])
{
    if ( argc != 3 ) {
        fprintf(stderr, "usage: dsc_extractor <path-to-cache-file> <path-to-device-dir>\n");
        return 1;
    }

    //void* handle = dlopen("/Volumes/my/src/dyld/build/Debug/dsc_extractor.bundle", RTLD_LAZY);
    void* handle = dlopen("/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle", RTLD_LAZY);
    if ( handle == NULL ) {
        fprintf(stderr, "dsc_extractor.bundle could not be loaded\n");
        return 1;
    }

    extractor_proc proc = (extractor_proc)dlsym(handle, "dyld_shared_cache_extract_dylibs_progress");
    if ( proc == NULL ) {
        fprintf(stderr, "dsc_extractor.bundle did not have dyld_shared_cache_extract_dylibs_progress symbol\n");
        return 1;
    }

    int result = (*proc)(argv[1], argv[2], ^(unsigned c, unsigned total) { printf("%d/%d\n", c, total); } );
    fprintf(stderr, "dyld_shared_cache_extract_dylibs_progress() => %d\n", result);
    return 0;
}
#endif
```

上面代码的大致逻辑是：在本地的Xcode.app中找到dsc_extractor.bundle这个动态库，加载到内存，然后通过dlsym拿到dyld_shared_cache_extract_dylibs_progress函数，然后用这个函数对dyld_shared_cache_arm64e文件进行提取。
另外，代码中清楚说明了，如何使用这个命令行工具
> usage: dsc_extractor <path-to-cache-file> <path-to-device-dir>
#### （3.3）. 准备extract代码
有了上面现成的代码，那么copy&paste一下，变成自己的dsc_extractor.cpp文件，如下
```cpp
#include <stdio.h>
#include <stddef.h>
#include <dlfcn.h>

typedef int (*extractor_proc)(const char* shared_cache_file_path, const char* extraction_root_path,void (^progress)(unsigned current, unsigned total));

int main(int argc, const char* argv[])
{
    if ( argc != 3 ) {
        fprintf(stderr, "usage: dsc_extractor <path-to-cache-file> <path-to-device-dir>\n");
        return 1;
    }

    //void* handle = dlopen("/Volumes/my/src/dyld/build/Debug/dsc_extractor.bundle", RTLD_LAZY);
    void* handle = dlopen("/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle", RTLD_LAZY);
    if ( handle == NULL ) {
        fprintf(stderr, "dsc_extractor.bundle could not be loaded\n");
        return 1;
    }

    extractor_proc proc = (extractor_proc)dlsym(handle, "dyld_shared_cache_extract_dylibs_progress");
    if ( proc == NULL ) {
        fprintf(stderr, "dsc_extractor.bundle did not have dyld_shared_cache_extract_dylibs_progress symbol\n");
        return 1;
    }

    int result = (*proc)(argv[1], argv[2], ^(unsigned c, unsigned total) { printf("%d/%d\n", c, total); } );
    fprintf(stderr, "dyld_shared_cache_extract_dylibs_progress() => %d\n", result);
    return 0;
}
```
编译之前，检查下这个路径是否有dsc_extractor.bundle文件
```shell
$ ls -l /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle
-rwxrwxr-x  1 wesley_chen  staff  567024 Apr 10  2021 /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle
```
#### （3.4）. 生成dsc_extractor命令行工具
执行下面的命令
```shell
$ clang++ -o dsc_extractor dsc_extractor.cpp
```
如果顺利的话，会得到名为dsc_extractor的可执行文件。
### (4) 提取dyld_shared_cache_arm64e文件
执行下面命令
```shell
$ ./dsc_extractor dyld_shared_cache_arm64e arm64e/
0/2369
1/2369
2/2369
3/2369
4/2369
5/2369
...
dyld_shared_cache_extract_dylibs_progress() => 0
```
如果顺利的话，会输出进度，并打印“dyld_shared_cache_extract_dylibs_progress() => 0”。
注意：
> 如果没有进度，仅打印“dyld_shared_cache_extract_dylibs_progress() => 0”，说明存在问题。
> 很可能提取的dyld_shared_cache_arm64e文件的版本，要高于Xcode的版本。比如说上面固件是15.0.1，但是Xcode版本还是12.5，不是Xcode 13.1。尽可能使用最新版本的Xcode，然后执行dsc_extractor工具
### (5) 放置Symbol文件
按照iOS DeviceSupport规范，添加2个文件
* `.finalized`。这个文件copy下已有的，就可以了
* `Info.plist`。这个文件自己修改下，如下
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>DSC Extractor Version</key>
	<string>dyld-852.2</string>
	<key>DateCollected</key>
	<date>2021-11-04T16:25:01Z</date>
	<key>Version</key>
	<string>15.0.1 (19A348)</string>
</dict>
</plist>
```
将文件夹重命名为15.0.1 (19A348) arm64e，放置到~/Library/Developer/Xcode/iOS DeviceSupport。
## 4.3、MacOS系统的Symbol符号
在Apple M1芯片推出后，MacOS系统支持arm64结构，某些iOS app可以运行在MacOS上。这些app使用的系统库是MacOS上的系统库，而不是iOS系统上的系统库。如果要符号化这些app出现的crash日志，则需要找到MacOS上的系统库。
参考官方论坛的[链接](https://forums.developer.apple.com/forums/thread/659324)，从Big Sur（MacOS 11）开始，引入dyld shared cache机制，所有系统库framework集成到一个单独二进制文件中，对应的描述，如下
> Big Sur introduces a dyld shared cache, where all of the system frameworks are built into a single optimized binary. The individual framework binaries are no longer present in the OS.
参考这个[SO](https://apple.stackexchange.com/questions/437912/obsolete-system-library-dyld-files-for-deletion)，dyld shared cache文件，名为dyld_shared_cache_arm64e，它位于下面文件夹
```
/System/Library/dyld
```
说明
> 参考这个[SO](https://stackoverflow.com/questions/64512628/trying-to-find-the-macos-framework-binaries)，MacOS 11后，位置可能存在变化。
由于SIP保护，默认是在该目录找不到dyld_shared_cache_arm64e文件。
> 在恢复模式下面，可以查看dyld_shared_cache_arm64e文件。
