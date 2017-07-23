#  方法

标签（空格分隔）： 黑板课

---

### **方法分类**

- 类方法，@classmethod
- 实例方法
- 静态方法，@staticmethod
- 特殊方法（魔法方法），init（可以是类方法或者实例方法）

### **方法和函数的区别**

- 调用是通过类和实例进行的，不能直接调用
- 有自己的特殊参数，实例方法self，类方法cls
- 有自己的声明语法，@classmethod，@statcimethod，____xxx____()
- 类方法绑定类，实例方法绑定实例对象，静态方法无绑定同函数类似，但是需要类和实例调用，特殊方法在某些场合下会被自动调用

### **例子**

```
#!/usr/bin/env python
# coding: utf-8
# http://stackoverflow.com/questions/12179271/python-classmethod-and-staticmethod-for-beginner

class Date(object):

    def __init__(self, day=0, month=0, year=0):
        self.day = day
        self.month = month
        self.year = year
        
    def __str__(self):
        return "{0}-{1}-{2}".format(self.year, self.month, self.day)
    
    @classmethod
    def from_string(cls, date_as_string):
        year, month, day = map(int, date_as_string.split('-'))
        date1 = cls(day, month, year)
        return date1
    
    @staticmethod
    def is_date_valid(date_as_string):
        year, month, day = map(int, date_as_string.split('-'))
        return day <= 31 and month <= 12 and year <= 3999  

    @staticmethod
    def millenium(month, day):
        return Date(month, day, 2000) 
        
        
class DateTime(Date):
    def __str__(self):
        return "{0}-{1}-{2} - 00:00:00PM".format(self.year, self.month, self.day)  

            
if __name__=="__main__":
    
    s='2012-09-11'
    if Date.is_date_valid(s):
        date1 = Date.from_string('2012-09-11')
        print date1
        date2 = DateTime.from_string('2012-09-11')
        print date2
        
    millenium_new_year1 = Date.millenium(1, 1)
    print millenium_new_year1
    
    millenium_new_year2 = DateTime.millenium(10, 10)
    print millenium_new_year2
```
PS:
```
- ____str____方法：用于print的时候自动调用显示定义的信息
- 对于类方法来讲，父类的方法的执行结果取决于谁来调用，子类调用，返回结果就是子类的
- 静态方法只需要导入参数，返回值只取决于方法定义，定义了返回谁，无论谁调用返回都一样
- 对于父类和子类而讲，实例化后自动调用的特殊方法，____str____或者____init____有一定区别。子类如果重新定义____str____,会覆盖掉父类的定义，通过查看实例.____str____,发现其**实例化后**绑定对象不同，通过查看实例.____str____.____func____发现其**实例化前**返回对象方法也是不同的；但是子类对____init____未重新定义，则实例化后绑定的方法对象是不相同的，但是实例化前返回的对象方法确实相同的

```
---

### **特殊方法**

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke          

class Point(object):
    def __init__(self,x,y):
        self.x = float(x)
        self.y = float(y)
    def __sub__(self, other):
        assert isinstance(other,Point)
        return Point(self.x-other.x,self.y-other.y)
    def __add__(self, other):
        assert isinstance(other,Point)
        return Point(self.x+other.x,self.y+other.y)
    def __mul__(self,factor):
        return Point(self.x*factor, self.y*factor)
    def __div__(self,factor):
        return Point(self.x/factor, self.y/factor)    
        
    @property    
    def xy(self):
        return (self.x,self.y)
    
    def __str__(self):
        return "x={0},y={1}".format(self.x,self.y)
    def __repr__(self):
        return str(self.xy)
        
if __name__ == '__main__':
       
    a = Point(50,60)
    b = Point(30,40)
    
    print a-b
    print a+b
    print a*2
    print a/2
```
**特殊方法分类**

```

- 属性访问： ____getattr____,____setattr____,____getattritube____
- 实例生成/类生成：____init____,____new____
- 数字计算：____add____,____sub____,____mul____,____div____,____pow____,____roound____ 加减乘除平方45度
- 调用方法：____str____,____repr____,____len____,____bool____
- 比较大小：____cmp____,____lt____,____le____,____eq____,____ne____,____gt____,____ge____
- 集合访问：____setslice____,____getslice____,____getitem____,____setitem____,____contains____
- 迭代器：____iter____,____next____

```


**比较大小方法含义**

```
 - l：less
 - t：than
 - e：equal
 - g：great
 - n：negative

____cmp____:compore,用于sort（）函数
____lt____: less than  小于
____le____: less equal 小于等于
____eq____: equal 等于
____ne____: negative equal 不等于
____gt____: great than 大于
____ge____: great equal 大于等于

**其它**
____new____:用于类生成，也可以用于实例生成
____len____:返回类包含的元素，以及长度
____bool____:返回类的真假
____setslice__,____getslice____:切片
____getitem____,____setitem____:访问字典的item
____contains____:包含
____iter____,____next____:迭代
____repr____:不定义时返回类的地址，定义以后，按照定义的方法显示
____str____:打印的时候就调用给输出给自己看，而____repr____是在解释器中显示的结果，不需要打印就调用

```


