---
layout: post
title: "iOS知识小集-170828"
date: 2017-09-04 14:12:44 +0800
comments: true
categories: iOS
keywords: sxgfxm,TableView Header / Footer Height,CNContactPickerViewController,CNContactStore,iOS本地化
description: sxgfxm,TableView Header / Footer Height,CNContactPickerViewController,CNContactStore,iOS本地化
---

## TableView Header / Footer Height
UITableView默认有section header/footer高度，设为0无效，最小可设为1。

## CNContactPickerViewController
```objective-c
-(void)pickContact{
  CNContactPickerViewController *controller = [[CNContactPickerViewController alloc] init];
  controller.delegate = self;
  [self presentViewController:controller animated:YES completion:nil];
}

- (void)contactPicker:(CNContactPickerViewController *)picker
    didSelectContactProperty:(CNContactProperty *)contactProperty {
  CNContact *contact = contactProperty.contact;
  NSString *name = [CNContactFormatter stringFromContact:contact style:CNContactFormatterStyleFullName];
  CNPhoneNumber *phoneValue = contactProperty.value;
  NSString *phoneNumber = phoneValue.stringValue;
  NSLog(@"%@--%@", name, phoneNumber);
}
```

<!-- more -->

## CNContactStore
info.plist添加`Privacy - Contacts Usage Description`。  

```objective-c
- (void)checkAuthorization {
  NSLog(@"Contact - Check Authorization");
  //  status
  CNAuthorizationStatus status =
      [CNContactStore authorizationStatusForEntityType:CNEntityTypeContacts];
  //  check authorization
  if (status != CNAuthorizationStatusAuthorized) {
    NSLog(@"Contact - Not Authorized");
    //  request access
    CNContactStore *store = [CNContactStore new];
    NSLog(@"Contact - Request Authorization");
    [store
        requestAccessForEntityType:CNEntityTypeContacts
                 completionHandler:^(BOOL granted, NSError *_Nullable error) {
                   if (granted) {
                     //  YES
                     NSLog(@"Contact - Authorized");
                     [self fetchContact];
                   } else {
                     //  No
                     NSLog(@"Contact - Not Authorized");
                   }
                 }];
  } else {
    NSLog(@"Contact - Authorized");
    [self fetchContact];
  }
}

- (void)fetchContact {
  NSLog(@"Contact - Fetch Contact");
  //  keys
  NSArray *keys = @[
    CNContactPhoneNumbersKey,
    CNContactGivenNameKey,
    CNContactFamilyNameKey
  ];
  //  request
  CNContactFetchRequest *request =
      [[CNContactFetchRequest alloc] initWithKeysToFetch:keys];
  //  store
  NSError *error = nil;
  CNContactStore *store = [CNContactStore new];
  [store
      enumerateContactsWithFetchRequest:request
                                  error:&error
                             usingBlock:^(CNContact *_Nonnull contact,
                                          BOOL *_Nonnull stop) {
                               //  contact
                               NSString *firstName = contact.familyName;
                               NSString *lastName = contact.givenName;
                               NSLog(@"%@ %@", firstName, lastName);
                               //  phone number
                               for (CNLabeledValue *labeledValue in contact
                                        .phoneNumbers) {
                                 CNPhoneNumber *phoneValue = labeledValue.value;
                                 NSString *phoneNumber = phoneValue.stringValue;
                                 NSString *label = [CNLabeledValue
                                     localizedStringForLabel:labeledValue
                                                                 .label];
                                 NSLog(@"%@ : %@", label, phoneNumber);
                               }
                               NSLog(@"");
                             }];
}
```



## iOS本地化
1. Project -> Localizations -> Add Chinese
2. New File -> Strings File
3. Strings File -> Utities -> Localization -> Add Chinese
4. Add Localized String

## 灯效说明
继承

## 智能家居控制
分解