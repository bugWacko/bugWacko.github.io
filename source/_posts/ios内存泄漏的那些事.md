---
title: ios内存泄漏的那些事
date: 2017-05-31 10:14:23
tags: 

- block 
- release 

---

从开始从事IOS开发以来，内存泄漏一直是自己需要很小心的处理的问题，因为这个东西一不小心就会出现，比较头疼。而且自己没有解决，其他人很少会帮你解决和排查。除非工程大面积优化，自己总结和参考了一些，内容相对比较常见，请大家指教：

+ <!-- more -->

### Block循环引用

block作为一个老生常谈的问题，但是一直也是大家比较容易出错的地方，关键就在于代码间是否出现了闭环，其中最为常见的就属MJRefresh了：

```
self.tableView.mj_header = [MJRefreshNormalHeader headerWithRefreshingBlock:^{
        self.page = 1;
        [self.dataArr removeAllObjects];
        [self loadData];
}];
```
我们来分析看看MJRefresh在什么情况下会出现造成循环引用问题：

我们可以再headerWithRefreshingBlock看到

```
#pragma mark - 构造方法
+ (instancetype)headerWithRefreshingBlock:(MJRefreshComponentRefreshingBlock)refreshingBlock
{
    MJRefreshHeader *cmp = [[self alloc] init];
    cmp.refreshingBlock = refreshingBlock;
    return cmp;
}
```
这里仅有三行代码，无非就是创建了下拉刷新部分View然后返回，这里比较重要的是**cmp.refreshingBlock = refreshingBlock**;这一句，这里的**refreshingBlock**是属于**MJRefreshHeader**的强引用属性，最后header会成为我们自己tableView的强引用属性mj_header，也就是说self.tableView强引用header, header强引用**refreshingBlock**，如果**refreshingBlock**里面强引用self，就成了循环引用，所以必须使用**weakSelf**，破掉这个循环。画图表示为：

![mjrefresh](https://raw.githubusercontent.com/bugWacko/bugwacko.github.io/master/projectFile/ios%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/1.png)

闭环过程：
**self --> self.tableView --> self.tableView.mj_header --> self.tableView.mj_header.refreshingBlock --> self**

解决方案：

```
__weak typeof(self) weakself = self; 
self.tableView.mj_header = [MJRefreshNormalHeader headerWithRefreshingBlock:^{
        __strong typeof(self) strongself = weakself;
        strongself.page = 1;
        [strongself.dataArr removeAllObjects];
        [strongself loadData];
}];
```
> strongself是为了防止内存提前释放，有兴趣的童鞋可深入了解，这里不做过多解释了。当然也可借助libextobjc库进行解决，书写为@weakify和@strongify会更方便些。

### delegate循环引用

delegate 同样存在闭环问题，但是delegate的问题很常见也是比较好解决的，如下：

```
//一般我们定义delegate都是会使用weak弱引用
@property (nonatomic, weak) id delegate;
```
下图比较形象的说明了使用weak修饰就是为了防止ViewController和UITableView相互强引用内存无法释放的问题：

![delegate](https://raw.githubusercontent.com/bugWacko/bugwacko.github.io/master/projectFile/ios%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/2.jpg)

### NSTimer循环引用
对于定时器NSTimer，使用不正确也会造成内存泄漏问题。这里简单举个例子，我们声明了一个类TestNSTimer，在其init方法中创建定时器执行操作。

```
@interface TestNSTimer ()
@property (nonatomic, strong) NSTimer *timer;
@end
@implementation TestNSTimer
 
- (instancetype)init {
    if (self = [super init]) {
        _timer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(timeRefresh:) userInfo:nil repeats:YES];
    }
    return self;
}
 
- (void)timeRefresh:(NSTimer*)timer {
    NSLog(@"TimeRefresh...");
}
 
- (void)cleanTimer {
    [_timer invalidate];
    _timer = nil;
}
 
- (void)dealloc {
    [super dealloc];
    NSLog(@"销毁");
    [self cleanTimer];
}
 
@end
```
在外部调用时，将其创建后5秒销毁。
```
TestNSTimer *timer = [[TestNSTimer alloc]init];
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [timer release];
    });
```
![timer](https://raw.githubusercontent.com/bugWacko/bugwacko.github.io/master/projectFile/ios%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/3.png)

从图中可以看出，定时器在无限执行下去，这个给内存很大的消耗。

我们都知道定时器使用完毕时需要将其停止并滞空，但cleanTimer方法到底何时调用呢？在当前类的dealloc方法中吗？并不是，若将cleanTimer方法调用在dealloc方法中会产生如下问题，当前类销毁执行dealloc的前提是定时器需要停止并滞空，而定时器停止并滞空的时机在当前类调用dealloc方法时，这样就造成了互相等待的场景，从而内存一直无法释放。因此需要注意cleanTimer的调用时机从而避免内存无法释放，如上的解决方案为将cleanTimer方法外漏，在外部调用即可。

如果是使用timer,有个比较好的处理方式是，谁执行谁释放，避免timer未能及时释放。

```
TestNSTimer *timer = [[TestNSTimer alloc]init];
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [timer cleanTimer];
        [timer release];
    });
```

`参考` `卖报的小画家Sure的简书` [http://www.cocoachina.com/ios/20170427/19135.html](http://www.cocoachina.com/ios/20170427/19135.html)

#### Happy reading!

---