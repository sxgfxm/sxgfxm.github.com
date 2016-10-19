---
layout: post
title: "iOS判断GPS坐标是否在中国"
date: 2016-10-19 10:29:23 +0800
comments: true
categories: iOS
---

## Background

GPS定位已经深入生活的方方面面。现实生活中存在着多种GPS坐标系：

- **WGS-84坐标系**（World Geodetic System——1984 Coordinate System），国际通用GPS坐标系。
- **GCJ-02坐标系**（Guojia Cehui Ju Coordinate System），中国专用坐标系，加入随机偏差，俗称火星坐标。
- **百度坐标系**，百度自己定义的坐标系。

在iOS应用开发中，**CoreLocation**返回**WGS坐标**，当定位在中国时，如果直接用**MKMapView**显示位置，由于中国地图使用**GCJ坐标**，会使定位出现偏差，达不到预期效果。为解决这一问题，在显示位置之前，需要判断**WGS坐标**是否在中国，并作出相应的坐标转换。

该问题抽象为**判断点是否在多边形内部**。

## 判断点是否在多边形内部算法

- 面积和判别法：判断目标点与多边形的每条边组成的三角形面积和是否等于该多边形，相等则在多边形内部。
- 夹角和判别法：判断目标点与所有边的夹角和是否为360度，为360度则在多边形内部。
- 引射线法：从目标点出发引一条射线，看这条射线和多边形所有边的交点数目。如果有奇数个交点，则说明在内部，如果有偶数个交点，则说明在外部。

算法原理在此不再赘述，主要关注算法的实现问题。

## 采用引射线法判断GPS坐标是否在中国

#### 构造中国大陆轮廓

因为**GCJ坐标系**不包含港澳台地区，所有选取中国大陆地区的采样点。

本文使用的采样点有限，某些边界可能存在偏差，仅供参考。

```objective-c
//  中国大陆多边形，用于判断坐标是否在中国
//  因为港澳台地区使用WGS坐标，所以多边形不包含港澳台地区
+ (NSMutableArray *)polygonOfChina {
  static NSMutableArray *polygonOfChina = nil;
  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    polygonOfChina = [[NSMutableArray alloc] init];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(49.1506690000,
                                                     87.4150810000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(48.3664501790,
                                                     85.7527085300)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(47.0253058185,
                                                     85.3847443554)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(45.2406550000,
                                                     82.5214000000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(44.8957121295,
                                                     79.9392351487)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(43.1166843846,
                                                     80.6751253982)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(41.8701690000,
                                                     79.6882160000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(39.2896190000,
                                                     73.6171080000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(34.2303430000,
                                                     78.9155300000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(31.0238860000,
                                                     79.0627080000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(27.9989800000,
                                                     88.7028920000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(27.1793590000,
                                                     88.9972480000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(28.0969170000,
                                                     89.7331400000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(26.9157800000,
                                                     92.1615830000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(28.1947640000,
                                                     96.0986050000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(27.4094760000,
                                                     98.6742270000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(23.9085500000,
                                                     97.5703890000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(24.0775830000,
                                                     98.7846100000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.1375640000,
                                                     99.1893510000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(21.1398950000,
                                                     101.7649720000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.2746220000,
                                                     101.7281780000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(23.2641940000,
                                                     105.3708430000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.7191200000,
                                                     106.6954480000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(21.9945711661,
                                                     106.7256731791)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(21.4847050000,
                                                     108.0200530000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(20.4478440000,
                                                     109.3814530000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(18.6689850000,
                                                     108.2408210000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(17.4017340000,
                                                     109.9333720000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(19.5085670000,
                                                     111.4051560000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(21.2716775175,
                                                     111.2514995205)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(21.9936323233,
                                                     113.4625292629)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.1818312942,
                                                     113.4258358111)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.2249729295,
                                                     113.5913115000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.4501912753,
                                                     113.8946844490)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.5959159322,
                                                     114.3623797842)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.4334610000,
                                                     114.5194740000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(22.9680954377,
                                                     116.8326939975)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(25.3788220000,
                                                     119.9667980000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(28.3261276204,
                                                     121.7724402562)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(31.9883610000,
                                                     123.8808230000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(39.8759700000,
                                                     124.4695370000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(41.7350890000,
                                                     126.9531720000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(41.5142160000,
                                                     128.3145720000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(42.9842081790,
                                                     131.0676468344)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(45.2690810000,
                                                     131.8468530000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(45.0608370000,
                                                     133.0610740000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(48.4480260000,
                                                     135.0111880000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(48.0054800000,
                                                     131.6628800000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(50.2270740000,
                                                     127.6890640000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(53.3516070000,
                                                     125.3710040000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(53.4176040000,
                                                     119.9254040000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(47.5590810000,
                                                     115.1421070000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(47.1339370000,
                                                     119.1159230000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(44.8256460000,
                                                     111.2786750000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(42.5293560000,
                                                     109.2549720000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(43.2598160000,
                                                     97.2967290000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(45.4247620000,
                                                     90.9680590000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(47.8075570000,
                                                     90.6737020000)]];
    [polygonOfChina
     addObject:[NSValue valueWithCGPoint:CGPointMake(49.1506690000,
                                                     87.4150810000)]];
  });
  return polygonOfChina;
}
```

#### 采用引射线法判断

```objective-c
/**
 *  判断是不是在中国
 *  用引射线法判断 点是否在多边形内部
 *  算法参考：http://www.cnblogs.com/luxiaoxun/p/3722358.html
 */
+ (BOOL)isLocationOutOfChina:(CLLocationCoordinate2D)location {
  CGPoint point = CGPointMake(location.latitude, location.longitude);
  BOOL oddFlag = NO;
  NSInteger j = [self polygonOfChina].count - 1;
  for (NSInteger i = 0; i < [self polygonOfChina].count; i++) {
    CGPoint polygonPointi = [[self polygonOfChina][i] CGPointValue];
    CGPoint polygonPointj = [[self polygonOfChina][j] CGPointValue];
    if (((polygonPointi.y < point.y && polygonPointj.y >= point.y) ||
         (polygonPointj.y < point.y && polygonPointi.y >= point.y)) &&
        (polygonPointi.x <= point.x || polygonPointj.x <= point.x)) {
      oddFlag ^= (polygonPointi.x +
                  (point.y - polygonPointi.y) /
                  (polygonPointj.y - polygonPointi.y) *
                  (polygonPointj.x - polygonPointi.x) <
                  point.x);
    }
    j = i;
  }
  return !oddFlag;
}
```

#### 测试结果

 ![result](../images/Map/result.png)

如上图所示，红色多边形为所构造的中国大陆轮廓。随机生成经纬度坐标进行测试，如果在中国，标记为红色；如果不在中国，标记为蓝色。

## 参考资料

[阿凡卢博客](http://www.cnblogs.com/luxiaoxun/p/3722358.html)

[Github源码地址](https://github.com/TinyQ/TQLocationConverter)

