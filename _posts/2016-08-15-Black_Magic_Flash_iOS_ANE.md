---
layout: post
title: "黑魔法之 iOS Flash ANE 打包"
date: 2014-08-15 02:09:46 +0800
categories: iOS
keywords: iOS, SDK, Flash, ANE
---

MA6-僵尸 使用Flash Air开发，故iOS SDK需包装一个Flash ANE版本。

用到的环境有：Flash Builder 4.7、Xcode 5.1.1、iOS SDK 7.1

## Flash Flex 项目 ##

在Flash Buildder中建立一个 **Flex手机项目库** ，新建一个.as脚本，实现as的wrapper。
下面给了个简化的项目示例，注意其中用于实现回调的onStatus和初始化函数NtUniSdk4Flash。

 <!-- more -->

	package com.netease.ntunisdk
	{
		import flash.events.EventDispatcher;
		import flash.events.StatusEvent;
		import flash.external.ExtensionContext;

		public class NtUniSdk4Flash extends EventDispatcher
		{
			private static var _instance:NtUniSdk4Flash;
			private var extContext:ExtensionContext;

			public function ntInit():void
			{
				extContext.call( "__NtSdkMgr_ntInit" );
			}

			public static function get instance():NtUniSdk4Flash {
				if ( !_instance ) {
					_instance = new NtUniSdk4Flash( new SingletonEnforcer() );
				}
				return _instance;
			}

			public function dispose():void {
				extContext.dispose();
			}

			private function onStatus( event:StatusEvent ):void {
				dispatchEvent( new SDKNotification(event.code, false, false) );
			}


			/**
		 	* Constructor.
		 	*/		
			public function NtUniSdk4Flash( enforcer:SingletonEnforcer ) {
				super();

				extContext = ExtensionContext.createExtensionContext( "com.netease.ntunisdk.ios.ane", "" );

				if ( !extContext ) {
					throw new Error( "iOS NtUniSdk native extension is not supported on this platform." );
				}

				extContext.addEventListener( StatusEvent.STATUS, onStatus );
			}
		}
	}

	class SingletonEnforcer {

	}


然后需要再建立一个extension.xml，示例再如下：

	<extension xmlns="http://ns.adobe.com/air/extension/3.1">
    	<id>com.netease.ntunisdk.ios.ane</id>
    	<versionNumber>0.0.1</versionNumber>
    	<platforms>  
			<platform name="iPhone-ARM">
            	<applicationDeployment>
                	<nativeLibrary>liblibNtUniSdk4Flash.a</nativeLibrary>
                	<initializer>ExtInitializer</initializer>
            	</applicationDeployment>
        	</platform>
    	</platforms>
	</extension>

因为我们需要的只是iphone版本的ANE，所以只有iPhone-ARM一个平台。com.netease.ntunisdk.ios.ane和上面as代码中的ExtensionContext.createExtensionContext参数是对应的。

然后，在菜单里，选择 项目 -> 构建项目，编译项目。如果你的构建项目事灰色的，那是打开了自动构建。把 项目 -> 自动构建 取消掉。

这时候，可以等到 **产出1. 一个.swc文件** 。


## Xcode 静态库项目 ##

建立一个Coco Touch Static Library, 在Air SDK里找到 FlashRuntimeExtensions.h，添加到项目中。

新建一个.m文件，下面给出项目简化版的示例：

	//
	//  libNtUniSdk4Flash.m
	//  libNtUniSdk4Flash
	//
	//  Created by Huang Quanyong on 14-8-7.
	//  Copyright (c) 2014年 Stupid Dumb Kids. All rights reserved.
	//

	#import "FlashRuntimeExtensions.h"
	#import "UniHead.h"

	#define STRING_BUFFER_SIZE 1024*128

	@interface __NtNotificationWrapper : NSObject

	@end
	static __NtNotificationWrapper *__inst = nil;
	static FREContext __context = NULL;

	@implementation __NtNotificationWrapper

	+ (void) initialize
	{
    	if (__inst) {
        	return;
    	}

   		__inst = [[__NtNotificationWrapper alloc] init];

    	//初始化通知
    	[[NSNotificationCenter defaultCenter] addObserver:__inst selector:@selector(finishInitNotification:) name:NT_NOTIFICATION_FINISH_INIT object:nil];

    }

	//初始化完成处理
	- (void)finishInitNotification:(NSNotification *)notification
	{
   		NSString* str = NT_NOTIFICATION_FINISH_INIT;
    	const uint8_t *code = (const uint8_t*) [str UTF8String];
    	FREDispatchStatusEventAsync(__context, code, code);
    	NSLog(@"[NtUniSdk] Notification finishInit.");
	}

	+ (void) doNothing{}

	@end

	FREObject __NtSdkMgr_ntInit(FREContext ctx, void* funcData, uint32_t argc, FREObject argv[])
	{
    	NSLog(@"[NtUniSdk] __NtSdkMgr_ntInit");
    	[__NtNotificationWrapper doNothing];
    	[NtSdkMgr ntInit];
    	return NULL;
	}

	// InitNativeCode()
	//
	// An InitNativeCode function is necessary in the Android implementation of this extension.
	// Therefore, an analogous function is necessary in the iOS implementation to make
	// the ActionScript interface identical for all implementations.
	// However, the iOS implementation has nothing to do.
	FREObject InitNativeCode(FREContext ctx, void* funcData, uint32_t argc, FREObject argv[]) {

    	NSLog(@"Entering InitNativeCode()");

    	// Nothing to do.

    	NSLog(@"Exiting InitNativeCode()");

    	return NULL;
	}

	// ContextInitializer()
	//
	// The context initializer is called when the runtime creates the extension context instance.

	void ContextInitializer(void* extData, const uint8_t* ctxType, FREContext ctx, uint32_t* numFunctionsToTest, const FRENamedFunction** functionsToSet) {

    	NSLog(@"Entering ContextInitializer()");

    	__context = ctx;

    	*numFunctionsToTest = 22;
    	FRENamedFunction* func = (FRENamedFunction*) malloc(sizeof(FRENamedFunction) * *numFunctionsToTest);

    	//Just for consistency with Android
    	func[0].name = (const uint8_t*) "__NtSdkMgr_ntInit";
    	func[0].functionData = NULL;
    	func[0].function = &__NtSdkMgr_ntInit;

    	*functionsToSet = func;

    	NSLog(@"Exiting ContextInitializer()");
	}

	// ContextFinalizer()
	//
	// The context finalizer is called when the extension's ActionScript code
	// calls the ExtensionContext instance's dispose() method.
	// If the AIR runtime garbage collector disposes of the ExtensionContext instance, the runtime also calls
	// ContextFinalizer().

	void ContextFinalizer(FREContext ctx) {

   	 	NSLog(@"Entering ContextFinalizer()");

    	// Nothing to clean up.

    	NSLog(@"Exiting ContextFinalizer()");

    	return;
	}

	// ExtInitializer()
	//
	// The extension initializer is called the first time the ActionScript side of the extension
	// calls ExtensionContext.createExtensionContext() for any context.

	void ExtInitializer(void** extDataToSet, FREContextInitializer* ctxInitializerToSet, FREContextFinalizer* ctxFinalizerToSet) {

    	NSLog(@"Entering ExtInitializer()");

    	*extDataToSet = NULL;
    	*ctxInitializerToSet = &ContextInitializer;
    	*ctxFinalizerToSet = &ContextFinalizer;

    	NSLog(@"Exiting ExtInitializer()");
	}

	// ExtFinalizer()
	//
	// The extension finalizer is called when the runtime unloads the extension. However, it is not always called.

	void ExtFinalizer(void* extData) {

    	NSLog(@"Entering ExtFinalizer()");

    	// Nothing to clean up.

    	NSLog(@"Exiting ExtFinalizer()");
    	return;
	}

注意中间消息的回调实现，调用FREDispatchStatusEventAsync，会触发as里的onStatus。

编译该项目，得到 **产出2.一个.a文件** ，即是extension.xml中的

	<nativeLibrary>liblibNtUniSdk4Flash.a</nativeLibrary>

## 编译ANE ###

编译ANE的过程简直就是黑魔法。

建立一个platform.xml，写入库依赖连接信息，以iTools SDK为例。

	<platform xmlns="http://ns.adobe.com/air/extension/3.1">
    	<description>An optional description.</description>
    	<copyright>2011 (optional)</copyright>
    	<sdkVersion>5.0.0</sdkVersion>
    	<linkerOptions>
        	<option>-ios_version_min 5.0</option>
        	<option>-framework AdSupport</option>
        	<option>-framework SystemConfiguration</option>
        	<option>-framework CFNetwork</option>
        	<option>-framework Security</option>
        	<option>-framework MobileCoreServices</option>
        	<option>-framework QuartzCore</option>
        	<option>-framework CoreTelephony</option>

        	<option>-framework StoreKit</option>
        	<option>-framework NtUniSdkiTools</option>

        	<option>-lc /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS7.1.sdk/usr/lib/libz.dylib</option>
    	</linkerOptions>
	</platform>

把产出1的.swc文件，后缀改为.zip，解压，得到catalog.xml和library.swf。

建立一个文件夹，命名为iPhone-ARM，放入三个文件，上一步所得的catalog.xml和library.swf、产出1.的.a文件。

新建一个目录，放入如下：

+ 上文的iPhone-ARM文件夹
+ 上文的platform.xml
+ 上文的extension.xml
+ 产出1.的NtUniSdk4Flash.swc
+ developer_identity.p12 用于签名的证书，密码为111111 (没有的话，可是用Flash Builder生成，请看后面步骤)


用以下指令编译ANE：

	"/Applications/Adobe Flash Builder 4.7/sdks/4.6.0/bin/adt" \
		-package -storetype pkcs12 -keystore developer_identity.p12 -storepass 111111 \
		-target com.netease.ntunisdk.ios.ane extension.xml -swc NtUniSdk4Flash.swc -platform iPhone-ARM -C iPhone-ARM  -platformoptions platform.xml  .

然后，你就生成了一个可用的ANE文件。

>没有developer_identity.p12证书

>请新建一个Flex手机工程，在工程上点击右键，选择属性，并选择 Flex构建打包 -> Apple iOS，在右边选择 数字签名 -> 创建，即可创建一个可用的数字签名证书。
