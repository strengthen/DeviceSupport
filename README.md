# English | [简体中文](./README-CN.md)

# I. Welcome to my App Store homepage to download my published apps.
<a href='https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397'><img height='70' alt='Download from AppStore' src='https://img.whalenas.com:283/image/202207141215375.png' /></a>
> [https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397](https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397)

# II. Appears when running Xcode:
### This operation can fail if the version of the OS on the device is incompatible with the installed version of Xcode. You may also need to restart your mac and device in order to correctly detect compatibility.
### This xxx is running iOS xxx, which may not be supported by this version of Xcode.
## 1. Click to download the specified iOS system support file: https://github.com/strengthen/DeviceSupport
## 2. Download the version you need listed in the library above, and unzip the version file into a folder.
## 3. Open [Finder], enter the shortcut key: [Command + Shift + G], enter the folder location: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport, where [Xcode. app] is the Xcode name. If you download multiple Xcodes, enter the name of the corresponding Xcode and put the decompressed folder into the DeviceSupport folder.
## 4. Restart Xcode.

# III. Xcode 15 has build problems in some Cocoa pods because the ".a" file is missing from the XcodeDefaults toolchain content. Here are all the missing files in Xcode 15.
## You can download and paste it into this path:
## /Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/arc/
## Note: If the lib folder does not exist, please create a folder named "arc".

# IV、Symbol file
## 4.1、Symbols
Symbols folders in ~/Library/Developer/Xcode/iOS DeviceSupport
## 4.2、Obtaining Symbol Files from Firmware
iOS firmware contains system symbol files. With some tools, symbol files can be extracted from the firmware.
### (1) Download the corresponding version of the firmware. The ipsw file is large, so you can choose to download it during a time when the network is idle. Firmware download URLs:
* [https://ipsw.me/product/iPhone](https://ipsw.me/product/iPhone)
* [https://www.i4.cn/firmware_iPhone_iPhone%20X_____.html](https://www.i4.cn/firmware_iPhone_iPhone%20X_____.html)
* [https://iosgujian.com/iPhone/iPhone11/iPhone12,1](https://iosgujian.com/iPhone/iPhone11/iPhone12,1)
### (2) Determine the location of the symbol file
Unzip the ipsw file. It is actually a zip file, so changing the file extension will extract it, resulting in the following files and folders:
```shell
$ iPhone14,5_15.0.1_19A348_Restore.ipsw ls -l
total 12722096
-rw-r--r--@ 1 wesley_chen staff 6251791817 Jan 9 2007 018-79898-003.dmg
-rw-r--r--@ 1 wesley_chen staff 121403931 Jan 9 2007 038-42528-641.dmg
-rw-r--r--@ 1 wesley_chen staff 123465243 Jan 9 2007 038-42637-643.dmg
-r--r--r--@ 1 wesley_chen staff 314550 Jan 9 2007 BuildManifest.plist
drwxr-xr-x@ 33 wesley_chen staff 1056 Jan 9 2007 Firmware
-r--r--r--@ 1 wesley_chen staff 1030 Jan 9 2007 Restore.plist
-rw-r--r--@ 1 wesley_chen staff 16727656 Jan 9 2007 kernelcache.release.iphone14
``` 
Sort by file, find the largest .dmg file, here it is 018-79898-003.dmg, open it.
Locate the file dyld_shared_cache_arm64e using the following path:
``shell
/System/Library/Caches/com.apple.dyld
``` 
There are two other files:
* /System/Library/Caches/com.apple.dyld/dyld_shared_cache_arm64e.1
* /System/Library/Caches/com.apple.dyld/dyld_shared_cache_arm64e.symbols
### (3) Obtain the dsc_extractor tool
Many tools can extract symbol files from the dyld_shared_cache_arm64e file, but the output extracted by dsc_extractor is the same as the output extracted by Xcode when connected to a real device. The description of this article [^1] is as follows:
> [dsc_extractor (source code)](https://opensource.apple.com/source/dyld/). More info [here](https://gist.github.com/NSExceptional/85527151eeec4b0640187a0a165da1cd). It produces the best results among all tools, but without branch islands workaround. Do note this is the exact same output provided by Xcode automatically.
Therefore, using the dsc_extractor tool is the best choice. However, the dsc_extractor tool is not readily available and needs to be compiled by yourself.
There are two articles [^2][^3] that introduce how to compile this tool. Follow their steps one by one.
### (3.1). Downloading the dyld file
Apple's opensource website provides the dyld file for download at: https://opensource.apple.com/tarballs/dyld/ Find the latest dyld file download address.
Execute the following commands:
```shell
$ wget https://opensource.apple.com/tarballs/dyld/dyld-852.2.tar.gz
$ tar -xvzf dyld-852.2.tar.gz
``` 
The dyld file may have a different file structure after decompression due to numerous version updates.
#### (3.2). Obtaining the extract code from dsc_extractor.cpp
Find the required dsc_extractor.cpp, located as follows:
``` 
dyld-852.2/dyld3/shared-cache/dsc_extractor.cpp
```
Note:
If you have dyld.xcodeproj, you can open it and search for dsc_extractor.cpp
In the code of dsc_extractor.cpp, find the following section of code that is commented out by the if conditional compilation, as follows:
``c
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
The general logic of the code above is: It locates the dynamic library `dsc_extractor.bundle` in the local Xcode.app, loads it into memory, then obtains the function `dyld_shared_cache_extract_dylibs_progress` via `dlsym`, and uses this function to extract the `dyld_shared_cache_arm64e` file.
In addition, the code clearly explains how to use this command-line tool:
> usage: dsc_extractor <path-to-cache-file> <path-to-device-dir>
#### (3.3). Preparing the extract code
With the existing code above, copy and paste it to create your own dsc_extractor.cpp file, as follows:
``cpp
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
```shell
$ ls -l /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle
-rwxrwxr-x  1 wesley_chen  staff  567024 Apr 10  2021 /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/usr/lib/dsc_extractor.bundle
```
#### (3.4). Generating the dsc_extractor command-line tool
Execute the following command:
``shell
$ clang++ -o dsc_extractor dsc_extractor.cpp
``` If successful, you will get an executable file named dsc_extractor.
### (4) Extract the dyld_shared_cache_arm64e file
Execute the following command:
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
If successful, the progress will be output, and "dyld_shared_cache_extract_dylibs_progress() => 0" will be printed.
Note:
> If there is no progress and only "dyld_shared_cache_extract_dylibs_progress() => 0" is printed, there is a problem.
> It is very likely that the version of the extracted dyld_shared_cache_arm64e file is higher than the Xcode version. For example, the firmware above is 15.0.1, but the Xcode version is still 12.5, not Xcode 13.1. Use the latest version of Xcode if possible, and then run the dsc_extractor tool.
### (5) Place Symbol files
According to the iOS DeviceSupport specification, add two files:
* `.finalized`. Copy the existing file for this.
* `Info.plist`. Modify this file as follows:
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
Rename the folder to 15.0.1 (19A348) Place the arm64e symbol in ~/Library/Developer/Xcode/iOS DeviceSupport.
## 4.3 Symbols in macOS
After the release of the Apple M1 chip, macOS supported the arm64 architecture, allowing some iOS apps to run on macOS. These apps use macOS system libraries, not iOS system libraries. To symbolize the crash logs of these apps, you need to locate the macOS system libraries.
Referring to the official forum post [link](https://forums.developer.apple.com/forums/thread/659324), starting with Big Sur (macOS 11), a dyld shared cache mechanism was introduced. All system library frameworks are integrated into a single binary file. The corresponding description is as follows:
Big Sur introduces a dyld shared cache, where all of the system frameworks are built into a single optimized binary. The individual framework binaries are no longer present in the OS.
Referring to this [SO](https://apple.stackexchange.com/questions/437912/obsolete-system-library-dyld-files-for-deletion), the dyld shared cache file is named dyld_shared_cache_arm64e, and it is located in the following folder:
```
/System/Library/dyld
```
Explanation Referring to this [SO](https://stackoverflow.com/questions/64512628/trying-to-find-the-macos-framework-binaries), the location may have changed in macOS 11 and later.
Due to SIP protection, the dyld_shared_cache_arm64e file is not found in this directory by default.
> You can view the dyld_shared_cache_arm64e file in recovery mode.