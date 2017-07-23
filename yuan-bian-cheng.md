# 元编程


---




```
Type(name,base,attrs)

name:类名称字符
bases：父类元祖
attrs：属性字典

A = type（‘A’，（object，），｛‘b’： 1｝）
a = A()
print A, a.b

def f(name, bases, attrs):
    attrs['c'] = 2
    return type(name, bases, attrs)

A = type（‘A’，（object，），｛‘b’： 1｝）




#!/usr/bin/env python
# coding: utf-8
#http://python-3-patterns-idioms-test.readthedocs.org/en/latest/Metaprogramming.html


def howdy(self, you):
    print("Howdy, " + you)

MyList = type('MyList', (list,), dict(x=42, howdy=howdy))

ml = MyList()
ml.append("Camembert")
print(ml)
print(ml.x)
ml.howdy("John")
```

```

#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke        

class PositiveInt1(int):
    def __init__(self, value):
        super(PositiveInt1,self).__init__(self, abs(value))

        
class PositiveInt2(int):
    def __new__(cls, value):
        return super(PositiveInt2,cls).__new__(cls, abs(value))     
        
        
class Float_Units( float ):
    def __new__(cls, value, unit ):
        obj= super(Float_Units,cls).__new__( cls, value )
        obj.unit= unit
        return obj   
                
           
        
if __name__=="__main__":    
    
    i = PositiveInt1(-3)
    print i        
    i = PositiveInt2(-3)
    print i  
    
    speed= Float_Units( 6.5, "km/s" )        
    print speed, speed.unit

```

ps:

```
- __new__分配空间，__init__实现
- __new__对不可变的类型进行拓展，

```
