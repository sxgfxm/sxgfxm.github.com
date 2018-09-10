---
layout: post
title: "iOS知识小集-180903"
date: 2018-09-10 14:34:51 +0800
comments: true
categories: iOS
keywords: sxgfxm, apple pay, touch id
description: sxgfxm, apple pay, touch id
---

## 集成Apple Pay

### 权限配置
按官方指导配置权限。

### 调用过程
引入`<PassKit/PassKit.h>`，权限判断，支付卡判断，设置商品参数，创建支付请求，显示支付界面，代理接收结果。  
<!-- more -->
```objective-c
- (void)useApplePay{
  //  权限判断
  if ([PKPaymentAuthorizationViewController canMakePayments]){
    //  支付卡判断
    if ([PKPaymentAuthorizationViewController canMakePaymentsUsingNetworks:@[PKPaymentNetworkVisa, PKPaymentNetworkChinaUnionPay, PKPaymentNetworkDiscover]]){
      //  设置商品参数
      NSDecimalNumber *amount = [NSDecimalNumber decimalNumberWithString:@"2.33"]
      PKPaymentSummaryItem *item = [PKPaymentSummaryItem summaryItemWithLabel:@"PJChao" amount:amount];
      //  支付请求
      PKPaymentRequest *request = [[PKPaymentRequest alloc] init];
      // 设置商户ID（merchant IDs）
      request.merchantIdentifier = @"com.mobvoi.ApplePayTest";
      // 设置国家代码(中国大陆)
      request.countryCode = @"CN";
      // 设置支付货币(人民币)
      request.currencyCode = @"CNY";
      // 设置商户的支付标准(3DS支付方式必须支持，其他方式可选)
      request.merchantCapabilities = PKMerchantCapability3DS;
      request.paymentSummaryItems = @[item];
      /**
       *  以上参数都是必须的
       *  以下参数不是必须的
       */
      // 设置收据内容
      request.requiredBillingAddressFields = PKAddressFieldAll;
      // 设置送货内容
      request.requiredShippingAddressFields = PKAddressFieldAll;
      // 设置送货方式
      PKShippingMethod *method = [PKShippingMethod summaryItemWithLabel:@"顺丰" amount:[NSDecimalNumber decimalNumberWithString:@"10.00"]];
      method.identifier = @"顺丰物流";
      method.detail = @"12小时到达";
      request.shippingMethods = @[method];
      //  显示支付页面
      PKPaymentAuthorizationViewController *paymentVC = [[PKPaymentAuthorizationViewController alloc] initWithPaymentRequest:request];
      paymentVC.delegate = self;
      if (paymentVC == nil) return;
      [self presentViewController:paymentVC animated:YES completion:nil];
    } else {
      //  跳转至银行卡设置界面
      [[[PKPassLibrary alloc] init] openPaymentSetup];
    }
  }
}

//  代理方法
#pragma mark - PKPaymentAuthorizationViewControllerDelegate
- (void)paymentAuthorizationViewController:(PKPaymentAuthorizationViewController *)controller
                       didAuthorizePayment:(PKPayment *)payment
                                completion:(void (^)(PKPaymentAuthorizationStatus status))completion
{
    /**
     *  在这里支付信息应发送给服务器/第三方的SDK（银联SDK/易宝支付SDK/易智付SDK等）
     *  再根据服务器返回的支付成功与否进行不同处理
     *  这里直接返回支付成功
     */
    completion(PKPaymentAuthorizationStatusSuccess);
}

- (void)paymentAuthorizationViewControllerDidFinish:(PKPaymentAuthorizationViewController *)controller
{
    // 点击支付/取消按钮隐藏界面
    [controller dismissViewControllerAnimated:YES completion:nil];
}
```

## 集成Touch ID
```objective-c
- (void)startLocalAuthentication{
  if (NSFoundationVersionNumber < NSFoundationVersionNumber_iOS_8_0) {
    NSLog(@"系统版本不支持TouchID");
  } else {
    LAContext *context = [[LAContext alloc] init];
    context.localizedFallbackTitle = @"请输入密码";
    NSError *error = nil;
    if ([context canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error]) {
      [context evaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics localizedReason:@"通过指纹登录" reply:^(BOOL success, NSError * _Nullable error) {
        if (success) {
          dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"验证成功");
          });
        } else {
          NSLog(@"验证失败");
        }
      }];
    } else {
      NSLog(@"当前设备不支持TouchID");
    }
  }
}
```
