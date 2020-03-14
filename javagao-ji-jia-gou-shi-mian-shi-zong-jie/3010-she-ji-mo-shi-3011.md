# 【设计模式】

## 参考教程

## 设计模式详解：[https://tuonioooo.gitbooks.io/application-framework/content/she-ji-mo-shi-pian.html](https://tuonioooo.gitbooks.io/application-framework/content/she-ji-mo-shi-pian.html)

**1. 说一下你熟悉的设计模式？**

* 单例模式：保证被创建一次，节省系统开销。
* 工厂模式（简单工厂、抽象工厂）：解耦代码。
* 观察者模式：定义了对象之间的一对多的依赖，这样一来，当一个对象改变时，它的所有的依赖者都会收到通知并自动更新。
* 外观模式：提供一个统一的接口，用来访问子系统中的一群接口，外观定义了一个高层的接口，让子系统更容易使用。
* 模版方法模式：定义了一个算法的骨架，而将一些步骤延迟到子类中，模版方法使得子类可以在不改变算法结构的情况下，重新定义算法的步骤。
* 状态模式：允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

**2. 简单工厂和抽象工厂有什么区别？**

* 简单工厂：用来生产同一等级结构中的任意产品，对于增加新的产品，无能为力。
* 工厂方法：用来生产同一等级结构中的固定产品，支持增加任意产品。
* 抽象工厂：用来生产不同产品族的全部产品，对于增加新的产品，无能为力；支持增加产品族。
