---
layout: post
title: "Core Data Tutorial iOS 10"
date: 2017-01-23 16:40:01 +0800
comments: true
categories: iOS
keywords: sxgfxm, Core Data, iOS 10, NSPersistentContainer
description: Core Data in iOS 10, NSPersistentContainer
---

iOS 10 CoreData使用总结。

<!--more-->

##Include Core Data
创建工程时选择包含CoreData，系统会自动生成相关文件。

##Add Entity
在xcdatamodeld文件中，添加Entity。  
Entity相当于表，其中Attributes相当于表中的记录，对应需要存储的数据。

##Create NSManagedObject Subclass
创建Entity对应的model。  
在Xcode8中，创建方式为Editor ———— Create NSManagedObject Subclass。  
注意，创建之前需要将数据库的Codegen设置为Manual/None，否则会报duplicate错误。

##Get NSPersistentContainer Object
iOS 10 对Core Data做了很大的优化和改进，大大简化了Core Data的使用。  
NSPersistentContainer是新添加的类，从此大部分情况下无需再和NSManagedObjectContext、NSPersistentStoreCoordinator打交道。  
获取NSPersistentContainer    



~~~objective-c
#import 'AppDelegate.h'

@interface ViewController ()

@property(nonatomic, strong) NSPersistentContainer *container;

@end

@implementation ViewController

-(void)viewDidLoad{
  [super viewDidLoad];
  self.container = ((AppDelegate *)[UIApplication sharedApplication].delegate).persistentContainer;
}

@end
~~~

##Add Record
导入通过系统创建的Create NSManagedObject Subclass，通过NSEntityDescription增添记录。  



~~~objective-c
#import "People+CoreDataProperties.h"

#define kEntityName @"People"

-(void)addRecord{
  People *people = [NSEntityDescription insertNewObjectForEntityForName:kEntityName inManagedObjectContext:self.container.viewContext];
  people.name = @"Tom";
  people.sex = YES;
  people.age = 23;
}
~~~

##Fetch Record
通过NSFetchRequest查询记录。  



~~~objective-c
-(void)fetchRecord{
  NSFetchRequest *fetchRequest = [NSFetchRequest fetchRequestWithEntityName:kEntityName];
  NSArray<People *> *results = [self.container.viewContext executeFetchRequest:fetchRequest error:nil];
  for (People *people in results) {
    NSLog(@"name %@", people.name);
    NSLog(@"sex  %@", people.sex);
    NSLog(@"age  %@", people.age);
  }
}
~~~

##Update Record
先fetch需要修改的记录，然后直接修改即可。  



~~~objective-c
-(void)updateRecord{
  NSFetchRequest *fetchRequest = [NSFetchRequest fetchRequestWithEntityName:kEntityName];
  NSPredicate *predicate = [NSPredicate predicateWithFormat:@"name == %@", @"Tom"];
  fetchRequest.predicate = predicate;
  NSArray<People *> *results = [self.container.viewContext executeFetchRequest:fetchRequest error:nil];
  for (People *people in results) {
    people.name = @"Lily";
  }
}
~~~

##Delete Record
先fetch需要删除的记录，然后通过NSManagedObjectContext删除即可。  



~~~objective-c
-(void)deleteRecord{
  NSFetchRequest *fetchRequest = [NSFetchRequest fetchRequestWithEntityName:kEntityName];
  NSPredicate *predicate = [NSPredicate predicateWithFormat:@"name == %@", @"Lily"];
  fetchRequest.predicate = predicate;
  NSArray<People *> *results = [self.container.viewContext executeFetchRequest:fetchRequest error:nil];
  for (People *people in results) {
    [self.container.viewContext deleteObject:people];
  }
}
~~~

## GitHub源码

[CoreDataDemo](https://github.com/sxgfxm/CoreDataDemo)

