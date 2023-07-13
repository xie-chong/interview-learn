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
