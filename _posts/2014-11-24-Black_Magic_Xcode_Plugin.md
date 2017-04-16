---
layout: post
title: "黑魔法之Xcode Plugin开发"
date: 2014-11-24 22:42:01 +0800
categories: iOS
keywords: Xcode, Plugin
---


Xcode 插件的黑科技

##1. 取得项目路径

先贴段代码：

    NSString *workspacePath = @"";

    // to find current project path
    NSArray *workspaceWindowControllers = [NSClassFromString(@"IDEWorkspaceWindowController") valueForKey:@"workspaceWindowControllers"];
    id workSpace;
    for (id controller in workspaceWindowControllers) {
        if ([[controller valueForKey:@"window"] isEqual:[NSApp keyWindow]]) {
            workSpace = [controller valueForKey:@"_workspace"];
        }
    }

    if (workSpace) {
        workspacePath = [[workSpace valueForKey:@"representingFilePath"] valueForKey:@"_pathString"];
    }

这段代码位于 BMPlugin.m 中，作用是取得当前 workspace，并读取其文件路径。

 <!-- more -->

> 由于 Xcode 插件开发基本无文档可查，这段代码也是从网上 copy 回来的。完全没有系统了解的方法。
>
> 值得注意的是，这些都不是公开的API，所以，Xcode 版本迭代时，注意检查兼容性。
>

##2. 标示支持的 Xcode 版本

插件项目的 info.plist 需要添加 DVTPlugInCompatibilityUUIDs 标示支持的 Xcode 版本。

当前 Xcode 版本的 DVTPlugInCompatibilityUUID 可在 Xcode.app/Contents/Info.plist 找到。

如果对应 Xcode 版本的 DVTPlugInCompatibilityUUID 没有被添加到 info.plist 中，那么该版本 Xcode 启动时不会加载插件。

##3. XCPluginHasUI NO

> Set XCPluginHasUI in Info.plist to YES to disable your plugin

记得这个属性 XCPluginHasUI 需要设置为 NO。

##4. 其他需要注意的

编译的时候记得把 .xib 界面文件添加到 Compile Sources 里面，否则，编译出来的包里面没有对应的 .nib 。
