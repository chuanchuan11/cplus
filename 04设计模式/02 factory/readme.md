>> 简单工厂模式

#### 概念

    通过专门定义一个**工厂类**来负责创建其它类的实例，被创建的实例通常**都有共同**的父类

#### 优缺点

    (1) 用户在使用时直接根据工厂类去创建所需的示例，无需了解这些对象是如何创建以及组织的
    
    (2) 有利用整个软件体系结构的优化
    
    (3) 工厂类集中了所有实例的创建逻辑，不符合开闭原则，当具体产品类不断增多时，工厂类也要做对应修改，扩展性不好，所以简单工厂模式，并不属于标准23种设计

#### 模式设计

    (1) 工厂类
    
        负责实现所有实例的内部逻辑，工厂类可以被外界直接调用
        
    (2) 抽象类
    
        所有对象的父类，它负责描述所有实例所共有的公共接口
    
    (3) 具体产品类

        简单工厂模式所创建的具体实例对象

![image](https://user-images.githubusercontent.com/42632290/232202396-654512b1-3cbf-4a53-a25c-e77c8315ed46.png)


#### 示例

```
附件
```

>> 工厂模式

#### 概念

    解决简单工厂模式中，所有实例都在一个工厂类中创建的紧耦合问题. 定义一个用于创建对象的接口有，让子类决定实例化哪一个类. 使类实例化延迟到子类[目的：解耦，手段：虚函数]

#### 优缺点

    (1) 隔离类对象的使用和具体类型之间的耦合关系.面对一个经常变化的具体类型，紧耦合关系(直接修改简单工厂中的factory源码)会导致软件的脆弱
    
    (2) 将所要创建的具体对象工作延迟到子类，从而实现一种**扩展**(而非更改的策略), 解决紧耦合关系
    
    (3) 缺点在于要求创建方法/参数相同

#### 模式设计

![image](https://user-images.githubusercontent.com/42632290/232262775-f6016dfd-89d9-4350-a312-45802d107c17.png)

#### 示例

```
附件
```



