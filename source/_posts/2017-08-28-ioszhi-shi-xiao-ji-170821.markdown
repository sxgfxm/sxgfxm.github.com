---
layout: post
title: "iOS知识小集-170821"
date: 2017-08-28 11:39:03 +0800
comments: true
categories: iOS
keywords: sxgfxm, IPA File,Insecure Local Data Storage,NSTimeZone,System Call,NSString to NSURL
description: sxgfxm, IPA File,Insecure Local Data Storage,NSTimeZone,System Call,NSString to NSURL
---

## ipa架构
~~~
IPA File
  Payload
    {appname}.app
      Application Binary
      Mobile Provision File
      Code Signature
      Bundled Resource File
  iTunesArtwork
  iTunesMetadata.plist
~~~

<!-- more -->

## Insecure Local Data Storage
1. PropertyList files  
2. NSUserDefaults class  
3. KeyChain  
4. CoreData and SQLite databases  

## Apple Data Protection API
1. Complete Protection (NSFileProtectionComplete)  
2. Protected Unless Open (NSFileProtectionCompleteUnlessOpen)  
3. Protected Until First User Authentication (NSFileProtectionCompleteUntilFirstUserAuthentication)  
4. No Protection (NSFileProtectionNone)  

## NSTimeZone
~~~
NSTimeZone *timeZone = [NSTimeZone localTimeZone];
//  Asia/Shanghai
NSString *name = timeZone.name;
//  GMT+8
NSString *abbreviation = timeZone.abbreviation;
~~~

## Prevent Buffer Overflows
1. Address Space Layout Randomization (ASLR)  
2. Automatic Reference Counting (ARC)  
3. Stack Protectors  

## System Call
~~~
-(void)makeCall:(NSString *)phone{
  NSString *phoneNumber = [@"telprompt://" stringByAppendingString:phone];
  [[UIApplication sharedApplication] openURL:[NSURL URLWithString:phoneNumber]];
}
~~~

## NSString to NSURL
~~~
NSString *encodeURL = [string stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet URLQueryAllowedCharacterSet]];
NSURL *url = [NSURL URLWithString:encodeURL];
~~~