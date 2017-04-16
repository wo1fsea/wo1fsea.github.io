---
layout: post
title: "U3D iOS SDK Wrapper"
date: 2014-08-17 18:47:54 +0800
categories: iOS
keywords: U3D, iOS, SDK
---

很多游戏使用U3D引擎，比如《忍者必须死》、《影之刃》... 所以，iOS NtUniSDK需要一个U3D的wrapper。

U3D的C#可以调用外部C接口，故需要为NtUniSDK包装一个C语言层，下面给出简化的项目示例代码。

 <!-- more -->

	#import "UnityAppController.h"

	#import "UniHead.h"

	#pragma mark __NtNotificationWrapper

	#define GameObject "Main Camera"

	#if defined(__cplusplus)
	extern "C"{
	#endif
    	extern void UnitySendMessage(const char *, const char *, const char *);
	#if defined(__cplusplus)
	}
	#endif


	@interface __NtNotificationWrapper : NSObject
	@end

	static __NtNotificationWrapper *__inst = nil;

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
   		NSLog(@"[NtUniSdk] Notification finishInit.");
    	UnitySendMessage(GameObject,"NT_NOTIFICATION_FINISH_INIT","");
	}


	+ (void) doNothing{}

	@end


	#pragma mark - __NtSdkMgr_X

	#if defined(__cplusplus)
	extern "C"{
	#endif

    char* __makeCString(NSString* string)
    {
        if (string == nil) {
            return NULL;
        }

        const char* cstring = [string cStringUsingEncoding:NSUTF8StringEncoding];

        if (NULL == cstring) {
            return NULL;
        }
        char* res = (char*)malloc(strlen(cstring)+1);
        strcpy(res, cstring);
        return res;
    }

    NSString* __makeNSString(const char* cstring)
    {
        if (cstring == NULL) {
            return nil;
        }

        NSString* nsstring = [[NSString alloc] initWithCString:cstring encoding:NSUTF8StringEncoding];

        return nsstring;
    }

    void __NtSdkMgr_ntInit()
    {
        [__NtNotificationWrapper doNothing];
        [NtSdkMgr ntInit];
    }

	#if defined(__cplusplus)
	}
	#endif

注意上述代码中，对Notification的传递使用了

	extern void UnitySendMessage(const char *, const char *, const char *);

调用GameObject上的回调函数。

回到U3D，新建一个C#文件，实现SDK的C# Wrapper。下面是简化的代码示例：


	using UnityEngine;

	using System;
	using System.Collections;
	using System.Collections.Generic;
	using System.Runtime.InteropServices;
	using System.Text;

	namespace NtUniSdk{
		namespace Unity3d{
			public class SdkU3d : MonoBehaviour
			{

				public static void ntInit()
				{
					__NtSdkMgr_ntInit();
				}

				[DllImport("__Internal")]
				private static extern void __NtSdkMgr_ntInit();

			};

		}
	}

在U3D中即可使用该Wrapper操作SDK。

在U3D中将工程导出为Xcode工程，向Xcode中添加上述完成的C包装层，同时，按文档正常接入NtUniSDK。
