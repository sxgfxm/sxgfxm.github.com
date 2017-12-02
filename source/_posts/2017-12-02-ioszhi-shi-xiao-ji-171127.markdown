---
layout: post
title: "iOS知识小集-171127"
date: 2017-12-02 18:08:07 +0800
comments: true
categories: iOS
keywords: sxgfxm,ContentInset,preferredStatusBarStyle无效,UITableView insert or delete or move cell,CLLocation逆向地理编码
description: sxgfxm,ContentInset,preferredStatusBarStyle无效,UITableView insert or delete or move cell,CLLocation逆向地理编码
---

## ContentInset
内容偏移，可用于下拉刷新调整界面位置。

## 嵌套UINavigationController的UIViewController设置preferredStatusBarStyle无效
如果`UIViewController`有`UINavigationController`，则会先调`UINavigationController`的`childViewControllerForStatusBarStyle`方法，该方法默认返回nil，所以子`UIViewController`设置的`preferredStatusBarStyle`无效。  
解决办法，继承`UINavigationController`，并重写`childViewControllerForStatusBarStyle`方法：  
```objective-c
- (UIViewController *)childViewControllerForStatusBarStyle{
    return self.topViewController;
}
```

<!-- more -->

## Present可Push的ViewController
需要在present之前用`UINavigationController`包装一下待present的`UIViewController`。


## UITableView insert or delete or move cell
```objective-c
#pragma mark - Edit
- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath{
  return YES;
}

- (UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath{
  return indexPath.section ? UITableViewCellEditingStyleInsert : UITableViewCellEditingStyleDelete;
}

- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath{
  switch (editingStyle) {
    case UITableViewCellEditingStyleDelete:{
      NSString *item = self.selectCategoies[indexPath.row];
      [self.selectCategoies removeObject:item];
      [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
      [self.unselectCategoies insertObject:item atIndex:0];
      [tableView insertRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:0 inSection:1]] withRowAnimation:UITableViewRowAnimationFade];
      break;
    }
    case UITableViewCellEditingStyleInsert:{
      NSString *item = self.unselectCategoies[indexPath.row];
      [self.unselectCategoies removeObject:item];
      [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
      [self.selectCategoies addObject:item];
      [tableView insertRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:self.selectCategoies.count - 1 inSection:0]] withRowAnimation:UITableViewRowAnimationFade];
      break;
    }
    case UITableViewCellEditingStyleNone:
      break;
  }
  [self uploadCategorySetting];
}

- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath{
  return @"删除";
}

#pragma mark - Move
- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath{
  if (indexPath.section == 0) {
    return YES;
  }else{
    return NO;
  }
}

- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath{
  [self.selectCategoies exchangeObjectAtIndex:sourceIndexPath.row withObjectAtIndex:destinationIndexPath.row];
  [tableView exchangeSubviewAtIndex:sourceIndexPath.row withSubviewAtIndex:destinationIndexPath.row];
  [self uploadCategorySetting];
}
```



## CLLocation逆向地理编码
逆向地理编码是一个异步操作，如需使用解析后的地址，需要在block回调时添加代码，而不是在`-locationManager:didUpdateLocations:`时添加代码。  

```
- (void)reverseGeocodeLocation:(CLLocation *)location {
  // 逆向地理编码
  CLGeocoder *geocoder = [[CLGeocoder alloc] init];
  [geocoder reverseGeocodeLocation:location
                 completionHandler:^(NSArray *placemarks, NSError *error) {
                   CLLocationCoordinate2D coordinate = location.coordinate;
                   // 中国坐标转换
                   if (![TQLocationConverter isLocationOutOfChina:coordinate]) {
                     coordinate = [TQLocationConverter transformFromWGSToGCJ:coordinate];
                     coordinate = [TQLocationConverter transformFromGCJToBaidu:coordinate];
                   }

                   if (error == nil && [placemarks count] > 0) {
                     CLPlacemark *placemark = [placemarks objectAtIndex:0];
                     [self.currentLocation setLocationInfoWithPlacemark:placemark
                                                               latitude:coordinate.latitude
                                                              longitude:coordinate.longitude];
                   } else {
                     DDLogInfo(@"No results were returned.");
                     [self.currentLocation setLocationInfoWithPlacemark:nil
                                                               latitude:coordinate.latitude
                                                              longitude:coordinate.longitude];
                   }

                   DDLogInfo(@"获取逆向地理编码成功 : %@", [self.currentLocation commaSeparatedAddress]);
                 }];
}
```

