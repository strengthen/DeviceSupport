# English | [简体中文](./README-CN.md)

# I. Appears when running Xcode:
### This operation can fail if the version of the OS on the device is incompatible with the installed version of Xcode. You may also need to restart your mac and device in order to correctly detect compatibility.
### This xxx is running iOS xxx, which may not be supported by this version of Xcode.
## 1. Click to download the specified iOS system support file: https://github.com/strengthen/DeviceSupport
## 2. Download the version you need listed in the library above, and unzip the version file into a folder.
## 3. Open [Finder], enter the shortcut key: [Command + Shift + G], enter the folder location: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport, where [Xcode. app] is the Xcode name. If you download multiple Xcodes, enter the name of the corresponding Xcode and put the decompressed folder into the DeviceSupport folder.
## 4. Restart Xcode.

# II. Xcode 15 has build problems in some Cocoa pods because the ".a" file is missing from the XcodeDefaults toolchain content. Here are all the missing files in Xcode 15.
## You can download and paste it into this path:
## /Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/arc/
## Note: If the lib folder does not exist, please create a folder named "arc".

# III. Welcome to my App Store homepage to download my published apps.
<a href='https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397'><img height='70' alt='Download from AppStore' src='https://img.whalenas.com:283/image/202207141215375.png' /></a>
> [https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397](https://apps.apple.com/cn/developer/%E5%BC%BA-%E6%9B%BE/id1453461397)