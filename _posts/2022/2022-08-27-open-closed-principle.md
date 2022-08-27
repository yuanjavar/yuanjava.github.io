---
layout: post
title: SOLID设计原则系列之--开闭原则
category: framework
tags: [framework]
excerpt: 开闭原则是什么？它在软件设计中起到了什么作用？
---
你好，我是Weiki，欢迎来到猿java。

真实工作中，你是否是这样运作：一个需求过来，把原来的代码改一遍，再一个需求过来，又把上一个需求的代码改一遍，很多重复的工作还是在日复一日的重复，有什么好的办法改善吗？
相信有经验的小伙伴一定听过：对扩展开发，对修改关闭。那么，你知道这句话的真正含义吗？今天我们就来聊聊开闭原则到底是怎么实现。

## 什么是开闭原则

开放封闭原则（Open–closed principle, OCP），是Bertrand Meyer 在1988年提出的，该设计原则认为：

> Software entities should be open for extension, but closed for modification.
>
> 软件实体应该对扩展开放，对修改关闭。


## 如何实现开闭原则

如下图，给出了电商领域的一个库存系统库存变更的简易模型：库存系统接收外部系统库存变更事件，然后对数据库中的库存进行修改。
![img.png](https://www.yuanjava.cn/assets/md/framework/stock.png)

假如现在WMS仓储系统有入库和出库事件会影响库存，很多人写出的代码是这样的：

```java
public class Stock {
  
    public void updateStock(String event){
        
        if("outOfStock" == event){
            // todo 出库事件   库存操作
            
        }else if("warehousing" == event){
            // todo 入库事件   库存操作
        }
    }
}
```

这时，需求来了：WMS仓储系统内部有盘点事件(盘盈/盘亏)，需要变更库存。于是，代码就会发展成下面这样：

```java
public class Stock {
  
    public void updateStock(String event){
        
        if("outOfStock" == event){
            // todo 出库事件   库存操作
            
        }else if("warehousing" == event){
            // todo 入库事件   库存操作
            
        }else if("panSurplus" == event){
            // todo 盘盈事件   库存操作
            
        }else if("loss" == event){
            // todo 盘亏事件   库存操作
        }
    }
}
```

很显然，这样代码的实现，每来一个需求，就需要增加一个else if分支，修改一次代码， 因此这个Stock类一直处于变更中，不稳定。

有没有什么好的办法，可以使得这个代码不用修改，但是能够灵活的被扩展？ 

这个时候就要搬出java的三大法宝：继承，实现，多态。

能不能把库存的修改抽象成一个接口

```java
public interface Event {
    public void updateStock(String event);
}
```

入库事件
```java
public class WarehousingEvent implements Event {
    public void updateStock(String event){
        // 业务逻辑
    }
}
```

出库事件
```java
public class OutOfStockEvent implements Event {
    public void updateStock(String event){
        // 业务逻辑
    }
}
```

xx事件
```java
public class XXXEvent implements Event {
    public void updateStock(String event){
        // 业务逻辑
    }
}
```

于是，Stock类中updateStock()库存变更逻辑就可以抽象成下面这样

```java

public class Stock {
  
    public void updateStock(String event){
        // 根据事件类型获取真实的实现类
        Event event = getEventInstance(event);
        // 库存变更操作
        event.updateStock();
    }
}

```

经过我们的抽象和改造之后，Stock.updateStock()类就稳定下来了，再也不需要每增加一个事件然后增加一个else if分支处理。这种抽象带来的好处是，每次有新的库存变更事件，只需要增加一个实现类，其他的都不需要更改，当库存事件不需要时只需要把实现类删除就ok了。


## 总结

开闭原则的核心是对扩展开放，对修改关闭。当你的业务需求一直都是在修改一段相同的代码时，你就得谨慎观察业务是不是有类似的问题，代码修改的理由是什么？它们之间是不是有一定的通性？能不能把这些变更点分离出来，让它通过扩展来实现而不是修改代码。
其实在业务开发中海油很多类似的场景，比如：电商系统中的会员系统，需要根据用户不同的登记计算不同的费用；网关系统中，根据不同的力度来实现限流，接口，ip，服务，集群；

可能有小伙伴会反驳，我业务场景有类似的场景，几个if else就搞定了，没有必要去搞这么复杂的设计。

本人建议：功夫在平时，功夫在细节。

  很多人总抱怨业务开发技术成长慢，特别是对于初级程序员，但是大部门人的起点都是业务的CRUD，如果你能在业务curd过程中想办法"挖掘"和"套用"某些
设计模式，通过这种长期的刻意练习，量变产生质变，慢慢的你就能领会这些经典设计原则的奥妙，无形中，你就和身边的小伙伴拉拉开了举例，终有一天你也能写出让人赏心悦目的代码。


## 最后
如果你觉得本文章对你有帮助，感谢转发给更多的好友，我们将为你呈现更多的干货， 欢迎关注公众号：猿java

