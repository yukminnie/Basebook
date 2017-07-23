# 继承



---


### **简单继承**

> 通过已经有的类生成新的类，新的类继承父类的所有属性和方法，但是新类又可以特殊化

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke 

class Employee(object):
    def __init__(self, name, job=None, pay=0): # class = data + logic
        self._name = name
        self._job = job
        self._pay = pay
    def giveRaise(self, percent):
        self._pay = int(self._pay * (1 + percent))
    def __str__(self):
        return '[Employee: %s, %s, %s]' % (self._name, self._job, self._pay)


class Manager(Employee):
    def __init__(self, name, pay): # Redefine constructor
        Employee.__init__(self, name, 'mgr', pay)
    def giveRaise(self, percent, bonus=.10):
        Employee.giveRaise(self, percent + bonus)

if __name__ == '__main__':
    a=Employee("xiaoli",'sw_engineer',10000)
    b=Employee("xiaowang",'hw_engineer',12000)
    c=Manager("xiaozhang",8000)

    members=[a,b,c]

    for member in members:
        member.giveRaise(0.1)
        print member
    
```

 - 例子中的giveRaise就是多态
 - 对于特殊方法____class____,我们使用对象.____class____, 表示对象是那个类生成的，对象.____class____.____base____,返回类的父类
 
 
#### **多重继承mro**

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke  

class A:
    a=1
    b=1

class B(A):
    a=2
    
class C(A):
    a=3
    b=3
    c=3
        
class D(B,C):
    pass    


    
if __name__=="__main__":
    d=D()
    

```

-  经典类可以通过import inspect，使用inspect.getmro（类名）来判断其优先继承自那个类，mro的的意思就是method resolution order，即方法解析顺序
- 新式类可以通过类名.____mro____来判断其类的继承，但是不能在实例中调用
- 优先继承对于经典类是深度优先（classic），对于新式类是广度优先（new），python2中有经典类，有新式类，但是python3全部为新式类
 
```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke  

class A(object):
    a=1
    b=1

class B(A):
    b=2
    
class C(A):
    a=3
    b=3
    c=3
        
class D(B,C):
    pass       
    
if __name__=="__main__":
    d=D()
    
    #print D.__mro__
```


**超级继承**

> 超级继承只存在与新式类中，使用超级继承后，调用顺序就是mro顺序
```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke  

class A:
    def test(self):
        print "A's test"

class B(A):
    def test(self):
        print "B's test"
        A.test(self)
        #super(B,self).test()
    
class C(A):
    def test(self):
        print "C's test"
        A.test(self)
        
class D(B,C):
    def test(self):
        print "D's test"
        B.test(self) 
        C.test(self)
    
if __name__=="__main__":
    a=D()
    a.test()
```

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke  

class A(object):
    def test(self):
        print "A's test"

class B(A):
    def test(self):
        print "B's test"
        super(B,self).test()
    
class C(A):
    def test(self):
        print "C's test"
        super(C,self).test()
        
class D(B,C):
    def test(self):
        print "D's test"
        super(D,self).test()

    
if __name__=="__main__":
    a=D()
    a.test()

```

### **继承和组合**

> 将已经有的类作为新类初始化的参数，把已经有的类作为一个list放入新类的成员中

**例子一**
```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke 

class Employee(object):
    def __init__(self, name, job=None, pay=0): # class = data + logic
        self._name = name
        self._job = job
        self._pay = pay
    def giveRaise(self, percent):
        self._pay = int(self._pay * (1 + percent))
    def __str__(self):
        return '[Employee: %s, %s, %s]' % (self._name, self._job, self._pay)


class Manager(Employee):
    def __init__(self, name, pay): # Redefine constructor
        super(Manager,self).__init__(name, 'mgr', pay)
    def giveRaise(self, percent, bonus=.10):
        super(Manager,self).giveRaise(percent + bonus)


class Department(object):
    def __init__(self, *args):
        self.members = list(args)

    def addMember(self, person):
        self.members.append(person)

    def showAll(self):
        for person in self.members:
            print person 
    def giveRaise(self,percent):
        for person in self.members:
            person.giveRaise(percent)

if __name__ == '__main__':
    a=Employee("xiaoli",'sw_engineer',10000)
    b=Employee("xiaowang",'hw_engineer',12000)
    c=Manager("xiaozhang",8000)

    d=Department(a,b,c)

    d.showAll()
    d.giveRaise(0.1)
    d.showAll()
```

**例子二**

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke        
        
class Point(object):
    def __init__(self,x,y):
        self.x = x
        self.y = y
    def __sub__(self, other):
        return Point(self.x-other.x,self.y-other.y)
    def __add__(self, other):
        return Point(self.x+other.x,self.y+other.y)
        
    @property    
    def xy(self):
        return (self.x,self.y)
    
    def __str__(self):
        return "x={0},y={1}".format(self.x,self.y)
    def __repr__(self):
        return str(self.xy)
        
class Shape(object):

    def __init__(self,points_list,**kwargs):
        for point in points_list:
            assert isinstance(point,Point),"input must be Point type"
        self._points=points_list[:]
        self._points.append(points_list[0])
        self._color=kwargs.get('color','#000000')
        
    def __str__(self):
        return str(self._points)
        
class RectAngle(Shape):
    def __init__(self,startPoint,w,h,**kwargs):
        self._w = w
        self._h = h
        super(RectAngle,self).__init__([startPoint,startPoint+Point(w,0),startPoint+Point(w,h),startPoint+Point(0,h)],**kwargs)
        
                
        
import math
class Circle(Shape):
    def __init__(self,center_p,radius,**kwargs):
        points_list = []
        self._radius = radius
        self._center_p=center_p
        N_circle=24
        for i in xrange(N_circle):
            points_list.append(center_p + Point(radius*math.cos(2.0*math.pi*i/N_circle),radius*math.sin(2.0*math.pi*i/N_circle)))
            
        super(Circle,self).__init__(points_list,**kwargs)
    


if __name__ == '__main__':
       
    prepare_draws=[]
    
    start_p = Point(50,60)
    a=RectAngle(start_p,100,80,color="#ff0000")
    prepare_draws.append(a)
       
    
    c=Circle(Point(200,200),100,color="#0000ff")
    prepare_draws.append(c) 
    
    for s in prepare_draws:
        print s,"\n"

```

- 没看懂

### **多态和重载**

- 多态：不同的类有相同的方法，相同的参数，但是功能却不同。调用时将一组对象放在集合里，无需判断具体的对象类型，同意调用

- 重载：相同方法的不同参数类型。对应python是args，kwargs

```
运算符重载
____add____,____sub____,____mul____,____div____
____bool____
____lt____,____gt____
____cmp____



例子一：

#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke        
        
class Point(object):
    def __init__(self,x,y):
        self.x = x
        self.y = y
    def __sub__(self, other):
        return Point(self.x-other.x,self.y-other.y)
    def __add__(self, other):
        return Point(self.x+other.x,self.y+other.y)
        
    @property    
    def xy(self):
        return (self.x,self.y)
    
    def __str__(self):
        return "x={0},y={1}".format(self.x,self.y)
    def __repr__(self):
        return str(self.xy)
        
class Shape(object):

    def __init__(self,points_list,**kwargs):
        for point in points_list:
            assert isinstance(point,Point),"input must be Point type"
        self._points=points_list[:]
        self._points.append(points_list[0])
        self._color=kwargs.get('color','#000000')   
    
    @property
    def area(self):
        return 0

        
class RectAngle(Shape):
    def __init__(self,startPoint,w,h,**kwargs):
        self._w = w
        self._h = h
        super(RectAngle,self).__init__([startPoint,startPoint+Point(w,0),startPoint+Point(w,h),startPoint+Point(0,h)],**kwargs)
        
    @property
    def area(self):
        return self._w*self._h

        
import math
class Circle(Shape):
    def __init__(self,center_p,radius,**kwargs):
        points_list = []
        self._radius = radius
        self._center_p=center_p
        N_circle=24
        for i in xrange(N_circle):
            points_list.append(center_p + Point(radius*math.cos(2.0*math.pi*i/N_circle),radius*math.sin(2.0*math.pi*i/N_circle)))
            
        super(Circle,self).__init__(points_list,**kwargs)
    
    @property    
    def area(self):
        return math.pi*self._radius**2

        
if __name__ == '__main__':
       
    prepare_draws=[]
    
    start_p = Point(50,60)
    a=RectAngle(start_p,100,80,color="#ff0000")
    prepare_draws.append(a)
    c=Circle(Point(200,200),100,color="#0000ff")
    prepare_draws.append(c) 
    
    
    for shape in prepare_draws:
        print shape.area
    

例子二：


#!/usr/bin/python
# -*- coding: utf-8 -*-
#copyRight by heibanke        

import math     

class Point(object):
    def __init__(self,x,y):
        self.x = x
        self.y = y
    def __sub__(self, other):
        return Point(self.x-other.x,self.y-other.y)
    def __add__(self, other):
        assert isinstance(other,Point),"input must be Point"
        return Point(self.x+other.x,self.y+other.y)
        
    @property    
    def xy(self):
        return (self.x,self.y)
    
    def __str__(self):
        return "x={0},y={1}".format(self.x,self.y)
    def __repr__(self):
        return str(self.xy)
        
        
        
class Shape(object):
    __slots__=('points','color')
    def __init__(self,points_list,**kwargs):
        for point in points_list:
            assert isinstance(point,Point),"input must be Point type"
        self.points=points_list[:]
        self.points.append(points_list[0])
        self.color=kwargs.get('color','#000000')
                    
    @property
    def area(self):
        return 0
    
    def __bool__(self):
        return self.area>0
        
    def __lt__(self,other):
        assert isinstance(other,Shape)
        return self.area<other.area
        
    def __gt__(self,other):
        assert isinstance(other,Shape)
        return self.area>other.area
        #return not self<other
        
    def __cmp__(self,other):
        if self>other:
            return 1
        elif self<other:
            return -1
        else:
            return 0
        
class RectAngle(Shape):
    def __init__(self,startPoint,w,h,**kwargs):
        self._w = w
        self._h = h
        super(RectAngle,self).__init__([startPoint,startPoint+Point(w,0),startPoint+Point(w,h),startPoint+Point(0,h)],**kwargs)
    
    @property
    def area(self):
        return self._w*self._h
        
class Circle(Shape):
    def __init__(self,center_p,radius,**kwargs):
        points_list = []
        self._radius = radius
        self._center_p=center_p
        N_circle=24
        for i in xrange(N_circle):
            points_list.append(center_p + Point(radius*math.cos(2.0*math.pi*i/N_circle),radius*math.sin(2.0*math.pi*i/N_circle)))
            
        super(Circle,self).__init__(points_list,**kwargs)
    
    @property
    def area(self):
        return math.pi*self._radius**2
        
    def __contains__(self,point):
        return ((point.x-self._center_p.x)**2+(point.y-self._center_p.y)**2)<self._radius**2
        
if __name__ == '__main__':
       
    prepare_draws=[]
    
    start_p = Point(50,60)
    a=RectAngle(start_p,100,80,color="#ff0000")
    prepare_draws.append(a)
    b=RectAngle(start_p,150,50,color="#00ff00")
    prepare_draws.append(b)    
    c=Circle(Point(200,200),50,color="#0000ff")
    prepare_draws.append(c) 
    
    print b<c

    for shape in prepare_draws:
        print shape.area
    
    print Point(200,210) in c
    print Point(100,210) in c    

    prepare_draws.sort()
    for shape in prepare_draws:
        print shape.area    


```