title: 关于AppStore IAP的新旧Receipt
date: 2014-08-17 21:27:40
tags: [iOS, IAP, Receipt]
---

iOS7.0后，SKPayment的property transactionReceipt变成了DEPRECATED。

建议使用新接口来实现receipt的获取。

	 [[[NSBundle mainBundle] appStoreReceiptURL] dataWithContentsOfURL:receiptURL];

如果使用了IAP，Apple官方的建议是使用下列代码来处理：

    NSData *receipt = nil;

    if (floor(NSFoundationVersionNumber) <= NSFoundationVersionNumber_iOS_6_1) {
        // Load resources for iOS 6.1 or earlier
        receipt = transaction.transactionReceipt;

    } else {
        NSURL *receiptURL = [[NSBundle mainBundle] appStoreReceiptURL];
        receipt = [NSData dataWithContentsOfURL:receiptURL];
    }

上面的代码很容易以为，新旧接口取得的receipt格式是一致的。其实不然。

 <!-- more -->

在NtUniSdk里面,iOS官方渠道的易信和网易通行证使用了IAP,然后使用了上述Apple官方建议的代码，结果就是receipt格式不一致，计费验证不通过，把接入SDK的G3坑了一顿。

原来旧接口返回的receipt如下：

	2014-07-24 15:53:51.284 [NetEase]iOSNtUniSdkFrameworkDemo[22831:4107] [NtUniSdk] IAP jsonResponse: {
    	receipt =     {
        	bid = "com.netease.sdk.test";
        	bvrs = "1.0";
        	"item_id" = 892617314;
        	"original_purchase_date" = "2014-07-24 07:43:14 Etc/GMT";
        	"original_purchase_date_ms" = 1406187794660;
        	"original_purchase_date_pst" = "2014-07-24 00:43:14 America/Los_Angeles";
        	"original_transaction_id" = 1000000117879059;
        	"product_id" = "com.netease.sdk.test.item2";
        	"purchase_date" = "2014-07-24 07:43:14 Etc/GMT";
        	"purchase_date_ms" = 1406187794660;
        	"purchase_date_pst" = "2014-07-24 00:43:14 America/Los_Angeles";
        	quantity = 1;
        	"transaction_id" = 1000000117879059;
        	"unique_identifier" = 0000b0196818;
        	"unique_vendor_identifier" = "FEF4F2DB-D07D-4B96-8F22-592A4B9CBAE7";
    	};
    	status = 0;
	}

而新接口返回的receipt：

	2014-07-24 17:11:16.475 [NetEase]iOSNtUniSdkFrameworkDemo[185:748f] [NtUniSdk] IAP jsonResponse: {
    	environment = Sandbox;
    	receipt =     {
        	"adam_id" = 0;
        	"application_version" = "1.0";
        	"bundle_id" = "com.netease.sdk.test";
        	"download_id" = 0;
        	"in_app" =         (
                        	{
                	"is_trial_period" = false;
                	"original_purchase_date" = "2014-07-24 09:11:00 Etc/GMT";
                	"original_purchase_date_ms" = 1406193060000;
                	"original_purchase_date_pst" = "2014-07-24 02:11:00 America/Los_Angeles";
                	"original_transaction_id" = 1000000117894869;
                	"product_id" = "com.netease.sdk.test.item2";
                	"purchase_date" = "2014-07-24 09:11:01 Etc/GMT";
                	"purchase_date_ms" = 1406193061000;
                	"purchase_date_pst" = "2014-07-24 02:11:01 America/Los_Angeles";
                	quantity = 1;
                	"transaction_id" = 1000000117894869;
            	}
        	);
        	"original_application_version" = "1.0";
        	"original_purchase_date" = "2013-08-01 07:00:00 Etc/GMT";
        	"original_purchase_date_ms" = 1375340400000;
        	"original_purchase_date_pst" = "2013-08-01 00:00:00 America/Los_Angeles";
        	"receipt_type" = ProductionSandbox;
        	"request_date" = "2014-07-24 09:11:17 Etc/GMT";
        	"request_date_ms" = 1406193077021;
        	"request_date_pst" = "2014-07-24 02:11:17 America/Los_Angeles";
    	};
    	status = 0;
	}

新旧receipt在验证API接口方面无变化，变化主要在于返回的jsonResponese格式。
新的jsonResponese格式官网文档如下：
>Receipt Fields
>https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ReceiptFields.html#//apple_ref/doc/uid/TP40010573-CH106-SW1

下面是程序中实际输出jsonResponese的Log，值得注意的有receipt字段中的in_app字段可能包含多个transaction的receipt。
客户端APP中的新接口每次读取将读取出还没读取过的所有receipt，当上一次transaction完成后，但没有成功调用到读取receipt接口时，即将出现多条，如下第二条Log所示。

	2014-07-24 16:06:52.077 [NetEase]iOSNtUniSdkFrameworkDemo[164:3e0b] [NtUniSdk] IAP jsonResponse: {
    	environment = Sandbox;
    	receipt =     {
        	"adam_id" = 0;
        	"application_version" = "1.0";
        	"bundle_id" = "com.netease.sdk.test";
        	"download_id" = 0;
        	"in_app" =         (
            	            {
                	"is_trial_period" = false;
                	"original_purchase_date" = "2014-06-23 06:59:55 Etc/GMT";
                	"original_purchase_date_ms" = 1403506795000;
                	"original_purchase_date_pst" = "2014-06-22 23:59:55 America/Los_Angeles";
                	"original_transaction_id" = 1000000114795007;
                	"product_id" = "com.netease.sdk.test.item1";
                	"purchase_date" = "2014-06-24 09:06:09 Etc/GMT";
                	"purchase_date_ms" = 1403600769000;
                	"purchase_date_pst" = "2014-06-24 02:06:09 America/Los_Angeles";
                	quantity = 1;
                	"transaction_id" = 1000000114795007;
            	},
                	        {
                	"is_trial_period" = false;
                	"original_purchase_date" = "2014-06-24 09:06:09 Etc/GMT";
                	"original_purchase_date_ms" = 1403600769000;
                	"original_purchase_date_pst" = "2014-06-24 02:06:09 America/Los_Angeles";
                	"original_transaction_id" = 1000000114938703;
                	"product_id" = "com.netease.sdk.test.item3";
                	"purchase_date" = "2014-06-24 09:06:09 Etc/GMT";
                	"purchase_date_ms" = 1403600769000;
                	"purchase_date_pst" = "2014-06-24 02:06:09 America/Los_Angeles";
                	quantity = 1;
                	"transaction_id" = 1000000114938703;
            	}
        	);
        	"original_application_version" = "1.0";
        	"original_purchase_date" = "2013-08-01 07:00:00 Etc/GMT";
        	"original_purchase_date_ms" = 1375340400000;
        	"original_purchase_date_pst" = "2013-08-01 00:00:00 America/Los_Angeles";
        	"receipt_type" = ProductionSandbox;
        	"request_date" = "2014-07-24 08:06:54 Etc/GMT";
        	"request_date_ms" = 1406189214105;
        	"request_date_pst" = "2014-07-24 01:06:54 America/Los_Angeles";
   		};
    	status = 0;
	}

Apple的IAP一直存在一个比较蛋疼的问题，在IAP的交易订单上没有填写额外信息的地方。由于存在网游的账号和AppStore支付账号两套系统，道具发放一直存在着可能在支付过程意外中断而导致漏洞的情况。iOS7.0在SKPayment中提供了一个applicationUsername属性用于填写用户信息，不过鉴于iOS7.0－的系统中没有该属性，在短时间内对这种意外情况改善也不大。
