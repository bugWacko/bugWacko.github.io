---
title: iOS - Logging only once for UICollectionViewFlowLayout cache mismatched frame
date: 2017-06-23 10:14:23
tags: 

- UICollectionView 
- UICollectionViewFlowLayout

---

## 前言
最近在使用UICollectionView制作横向滚动缩放例子，在技术缩放算法中需要用到layoutAttributesForElementsInRect，代码使用方式如下：

```
NSArray *array = [super layoutAttributesForElementsInRect:rect];
```

很不幸，出现了警告，警告内容为

> Logging only once for UICollectionViewFlowLayout cache mismatched frame

> This is likely occurring because the flow layout subclass CXSlideCollectionViewFlowLayout is modifying attributes returned by UICollectionViewFlowLayout without copying them

从上文可以看出，警告告诉我们，UICollectionViewFlowLayout的返回对象，你需要对其进行copy操作，于是将代码改成：

```
NSArray *array = [[NSArray alloc] initWithArray:[super layoutAttributesForElementsInRect:rect] copyItems:YES];
```

警告就消除了。

#### Happy reading!

---