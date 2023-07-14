## 实例变量的使用

```
import time


class Coffee:
    water = 0
    milk = 0
    sugar = 0

    def add_water(self):
        # coffee_attributes = dir(Coffee)
        # print(coffee_attributes)
        self.hot_water = 10

        time.sleep(2)

        print(f'add_water is {self.hot_water}')
        # coffee_attributes = dir(Coffee)
        # print(coffee_attributes)

    def show_details(self):
        print(f'show_details----{self.hot_water}')


if __name__ == '__main__':
    pass
    mocha = Coffee()
    mocha.add_water()
    mocha.show_details()

    mabo = Coffee()
    mabo.show_details()
```

**上面代码，使用 self.hot_water = 10 有什么不妥之处？**

在给self.hot_water赋值为10的操作中，会创建一个新的实例变量self.hot_water，而不是修改已经存在的类变量或实例变量。因此，在add_water方法中使用self.hot_water = 10这样的赋值语句可能会导致以下问题：

1. 创建了一个新的实例变量：self.hot_water = 10会在实例对象中创建一个名为hot_water的新变量，并赋值为10。这个实例变量将会屏蔽掉类变量和其他同名实例变量的访问。

2. 类变量未被修改：在Coffee类定义中，hot_water是一个类变量，它与所有实例共享。但是，在add_water方法中使用self.hot_water = 10并没有修改类变量的值，**只是创建了一个与类变量同名但作用域仅限于该实例的变量**。

上面示例中，如果加入构造方法，将不会报错
```
    def __init__(self) -> None:
        self.hot_water =100
```


## 类的属性是定义在__init__ 方法里面还是外面？

类的属性可以定义在 __init__ 方法内部或外部，具体取决于您的需求和设计

1. 定义在 __init__ 方法内部：如果一个属性需要在每个对象创建时都有不同的初始值，或者需要根据传入的参数进行计算或设置，那么可以将属性定义在 __init__ 方法内部。这样，在每次创建对象时，__init__ 方法都会被调用，将相应的属性赋予新创建的对象。

```
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

```

在上述示例中，name 和 age 属性被定义在 __init__ 方法内部，每次创建 Person 对象时都会为这两个属性赋予不同的值。

2. 定义在 __init__ 方法外部：如果一个属性的值对于每个对象而言都是相同的，那么可以将其定义在 __init__ 方法外部，作为类的属性。这样，在每个对象之间共享相同的属性值。

```
class Person:
    nationality = "Chinese"

    def __init__(self, name, age):
        self.name = name
        self.age = age

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

```

在上述示例中，nationality 属性被定义在 __init__ 方法外部，它是一个类级别的属性，所有的 Person 对象都会共享相同的 nationality 值。
