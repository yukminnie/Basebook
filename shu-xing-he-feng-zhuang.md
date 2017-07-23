# 属性和封装


---

### **经典类和新式类基本语法**

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
class A:
    #classic class
    """this is class A"""
    pass
    __slots__=('x','y')
    def test(self):
        # classic class test
        """this is A.test()"""
        print "A class"
class B(object):
    #new class
    """this is class B"""
    __slots__=('x','y')
    pass
    def test(self):
        # new class test
        """this is B.test()"""
        print "B class"

        
if __name__ == '__main__':
    a=A()
    b=B()
    print dir(a)
    print dir(b)

    #a.x=1
    #b.x=1

    #help(a)
    #help(b)
```
**经典类和新式类的区别**

```
__slots__
继承顺序，super
__new__
__getattribute__
```
- 经典类可以不指定父类，新式类需要指定父类
- #号注释为普通注释，三引号的注释会加入到帮助文档，使用help调用
- 槽方法（____slots____）定义后，经典类仍然可以无限调用方法，新式类只能调用槽中定义方法,此时字典属性会被隐藏，槽方法在经典类的帮助文档中不会体现
- 经典类使用dir方法打印后，只有doc，module两个类属性和手动定义的属性，新式类有以下类方法
```
__class__
__dict__
__getattribute__
__init__
__new__
__repr__
__str__

__setattr__
__format__
__doc__
__module__
__reduce__
__reduce_ex__
__sizeof__
__subclasshook__
__weakref__
__ribute__

以及自定义属性
```
---
### **类和实例属性**

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class Car(object):
    country = u'中国'
    def __init__(self, length, width, height, owner=None):
        self.owner = owner
        self.length = length
        self.width = width
        self.height = height
        self.country = "china"

if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    b = Car(2.2,2.4,2.5,u'小张')
    print a.owner, b.owner
    print a.country, b.country

    b.country = u'美国'

    print a.country, b.country
    print Car.country

    print "--------------------------"
    del a.country
    print a.country
    #del a.country
    #del Car.country
    #print a.country

```
**实例属性和类属性的区别**

- 实例属性通常定义在init函数中，类属性定义在init函数之外
- init为类的特殊方法，在类实例化的时候就会被调用，实例参数需要和init方法参数相一致，特殊方法通常都有这种隐性的调用时机
- owner = none是默认参数
- 实例属性可以和类属性重名，调用或更改重名的实例属性时，不会影响类属性；删除重名的实例属性后，调用实例属性将会调用重名类属性，调用结果还会随着类属性的更改而更改

```
删除或者更改都可以在实例的__dict__方法中体现
a.__dict__
b.__dict__

调用类的相应方法可以使用__class__.__dict__
a.__class____dict__
b.__class____dict__
```
- 描述符

---

### **私有属性**
> 不能直接访问，需要经过一定的处理才可以访问，对类可见，对外界不可见

```
a.lengh = 1
a.lengh = -100
a.lengh = 'aaaaaa'
不能保证其合理性，我们需要将其设置为私有，不能对其直接赋值，赋值需要自定义函数，不合法时将会报错

私有属性分类
__xxx   我们需要对其设定限制函数
_xxx    只起到提示功能
__xxx__ 系统定义的私有属性

```

**实例**
```

#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class Car(object):
    country = u'中国'
    def __init__(self, length, width, height, owner=None):
        self.__owner = owner
        
        assert length>0,"length must larger than 0"
        self._length = length
        self._width = width
        self._height = height

    def getOwner(self):
        return self.__owner
    def setOwner(self, value):
        self.__owner = value

    def getLength(self):
        return self._length
        
    def setLength(self,value):
        assert value>0,"length must larger than 0"
        self._length = value

if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    print a.getOwner()

    #a.setLength(-1)
```
 
 - 设置为第一类**双下划线私有属性**之后，可以通过 **实例.__类名____实例方法** 访问私有属性，因为此时实例被添加了 ____类名______实例方法属性，但是它的限制条件是在init方法中定义的，
 - 设置为第二类**单下划线私有属性**之后，我们仍可以通过普通方法访问，但是这是不合理的，我们应该将限制条件写在实例方法中
 - 私有属性的意义就是------**封装**，定义接口
 
---

### **装饰器描述符**

```
@propery   定义
@xxx.setter  设置
@xxx.deleter  删除

```


> 给实例方法定义了get和set方法，使实例方法变成了有get和set的对象，方法即对象

**示例代码**

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class Car(object):
    country = u'中国'
    def __init__(self, length, width, height, owner=None):
        self._owner = owner
        self._length = length
        self._width = width
        self._height = height

    @property
    def owner(self):
        return self._owner
    @owner.setter
    def owner(self, value):
        self._owner = value
    @owner.deleter
    def owner(self):
        self._owner = None
    
    @property
    def length(self):
        return self._length
    @length.setter
    def length(self, value):
        assert value>0,"length must larger than 0"
        self._length = value

if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    print a.owner
    del a.owner
    print a.owner
    a.length=-1

```

---

### **____getattr____**

- 在变量的dict和class_dict中没有找到你想要的东西，就会调用____getattr____
- 在对一个属性赋值的时候，就会调用____setattr____
- 删除一个属性的时候，就会调用____delattr____




```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class Car(object):
    country = u'中国'
    __slots__=('length','width','height','owner','__dict__')
    
    def __init__(self, length, width, height, owner=None):
        self.owner = owner
        self.length = length
        self.width = width
        self.height = height
        
    def __getattr__(self,name):
        print "__getattr__",name
        return self.__dict__.get(name,None)
        
    def __setattr__(self,name,value):
        print "__setattr__",name
        if name!='owner':
            assert value>0, name+" must larger than 0"
        self.__dict__[name]=value
        
    def __delattr__(self,name):
        print "__delattr__",name
        if name=='owner':
            self.__dict__[name]=None

            
if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    """
    print a.owner
    del a.owner
    print a.owner
    a.length=1
    
    print a.country
    a.country = 'china'
    print a.country
    """
    a.name = u"一汽"
```

### **____getattr____和____slots____的搭配使用**

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class Car(object):
    country = u'中国'
    __slots__=('length','width','height','owner','__dict__')
    
    def __init__(self, length, width, height, owner=None):
        self.owner = owner
        self.length = length
        self.width = width
        self.height = height
        
    def __getattr__(self,name):
        print "__getattr__",name
        assert name in self.__slots__, "Not have this attribute "+name
        return self.__dict__.get(name,None)
        
    def __setattr__(self,name,value):
        print "__setattr__",name
        assert name in self.__slots__, "Not have this attribute "+name
        
        if name!='owner':
            assert value>0, name+" must larger than 0"
        self.__dict__[name]=value
        
    def __delattr__(self,name):
        print "__delattr__",name
        assert name in self.__slots__, "Not have this attribute "+name
        if name=='owner':
            self.__dict__[name]=None

if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    """
    print a.owner
    del a.owner
    print a.owner
    a.length=1
    
    print a.country
    a.country = 'china'    
    print a.country
    
    a.name = u"一汽"
    """
```

**描述符**

- 非数据描述符只有 ____get____
- 数据描述符有 ____set____和____get____和____delect____
- 当你为一个类使用了描述符，此类将可以作为另一个类的属性。此时描述符定义在主类的类函数中，而主类的init函数初始化时，其属性将使用累函数中的描述符

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke

class PositiveNum(object):
    def __init__(self,value):
        self.val = value
 
    def __get__(self, instance, owner):
        # instance = a,b
        # owner = Car
        print "__get__",instance,owner
        return self.val
 
    def __set__(self, instance, value):
        # instance = a,b
        print "__set__",instance,value
        try:
            assert int(value)>0
            self.val = value
        except AssertionError:
            print "ERROR: "+str(value)+" is not positive number."
        except:
            print "ERROR: "+str(value)+" is not number value."  
            
    def __delete__(self,instance):
        print "__delete__",instance
        self.val = None

    #def __getattribute__(self,name):
        #print self, name
        
class Car(object):
    country = u'中国'
    length = PositiveNum(0)
    width = PositiveNum(0)
    height = PositiveNum(0)
    #__slots__=('owner','length','width','height')
    
    def __init__(self, length, width, height, owner=None):
        self.owner = owner
        self.length = length
        self.width = width
        self.height = height
        


if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    b = Car(2.2,2.4,2.5,u'小明')
    

    

```
 
- 此时访问a.length，b.length,将会返回相同的值，因为它们都通过了set函数，而PositiveNum是从属于主类Car的，length在主类Car中表现时类函数
 
 课件8
 
```
 #!/usr/bin/env python
# -*- coding: utf-8 -*-
#copyRight by heibanke
class PositiveNum(object):
    def __init__(self):
        self.default = 1
        self.data = {}
 
    def __get__(self, instance, owner):
        # instance = x
        # owner = type(x)
        print "__get__",instance,owner
        return self.data.get(instance, self.default)
 
    def __set__(self, instance, value):
        # instance = x
        print "__set__",instance,value
        try:
            assert int(value)>0
            self.data[instance] = value
        except AssertionError:
            print "ERROR: "+str(value)+" is not positive number."
        except:
            print "ERROR: "+str(value)+" is not number value."
            
    def __delete__(self,instance):
        print "__delete__",instance
        del self.data[instance]
        
class Car(object):
    country = u'中国'
    length = PositiveNum()
    width = PositiveNum()
    height = PositiveNum()
    __slots__=('owner','length','width','height')
    
    def __init__(self, length, width, height, owner=None):
        self.owner = owner
        self.length = length
        self.width = width
        self.height = height
        

if __name__ == '__main__':
    a = Car(1.2,1.4,1.5,u'黑板客')
    b = Car(2.2,2.4,2.5,u'小明')
    """
    print a.owner
    print b.length
    a.length=1
    
    print a.country
    #a.country = 'china'    
    print a.country
    
    a.name = u"一汽"
    """

```

### **____getattribute____**

-访问变量都会调用____getattribute____，包括装饰器描述符，实例属性，但是访问类属性，不会被调用


```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke        

class Car(object):
    country = u'中国'
    def __init__(self, length, width, height, owner=None):
        self._owner = owner
        self._length = length
        self._width = width
        self._height = height

    @property
    def owner(self):
        return self._owner
    @owner.setter
    def owner(self, value):
        self._owner = value
    @owner.deleter
    def owner(self):
        self._owner = None
    
    def __getattribute__(self, name):
        print "__getattribute__ ", self, name
        return object.__getattribute__( self, name )

        
        
if __name__=="__main__":    
    
    a = Car(1.2,1.4,1.5,u'黑板客')

    a._owner

```

