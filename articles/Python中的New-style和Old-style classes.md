###使用super()的错误

super函数是python中的一个内置函数,提供对继承的类的函数调用,特别是在子类中被overridden的夫类函数,比如 

    __init__()
    
最近在使用super函数的时候出现了个错误,例如下:

    >>> class Base:
    ...     def __init__(self):
    ...         self.num = 1
    ... 
    >>> class Next(Base):
    ...     def __init__(self):
    ...         super(Next, self).__init__()
    ... 
    >>> obj = Next()
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "<stdin>", line 3, in __init__
    TypeError: must be type, not classobj

可以看到抛出了参数类型错误的错误.一开始完全不知所措,然后将出错信息google了一下,找到了解决方式:

    >>> class Base(object):
    ...     def __init__(self):
    ...         self.num = 1
    ... 
    >>> class Next(Base):
    ...     def __init__(self):
    ...         super(Next, self).__init__()
    ... 
    >>> obj = Next()
    >>> obj.num
    1
    >>> 
    
简单地将Base继承object就可以解决这个错误.其实这是python中的NewStyle classes和OldStyle classes而导致的一个问题. super()函数只适用于NewStyle classes.

###Newstyle和Oldstyle

Python中,直至python2.1,类和类型是两种不相关的概念,例如:

    >>> class Test:
    ...     pass
    ... 
    >>> Test().__class__
    <class __main__.Test at 0xb77373ec>
    >>> type(Test())
    <type 'instance'>
    >>> type(type(Test()))
    <type 'type'>
    >>> 
    
在这里Test类是Oldstyle的类.可以看到,Test类的一个实例,它的类是Test,但是type却是instance.这是因为Oldstyle的类与类型是不统一的概念,Oldstyle的实例是独立于它们的类,由一个python内置类型instance实现的.

从2.2开始,python开始使用New-style来统一类和类型.对于一个New-style的类,它的实例的类型和类都是一致的.为了兼容之前的代码,在python2.2之后,默认的类定义还是Old-style的类.而一个New-style的类可以通过继承一个New-style的类或者在类继承中最顶端继承object来实现,如下:

    >>> class Test(object):
    ...     pass
    ... 
    >>> Test().__class__
    <class '__main__.Test'>
    >>> type(Test())
    <class '__main__.Test'>
    >>> type(type(Test()))
    <type 'type'>
    >>> 

New-style类的提出是为了统一python的对象模型.在python3中,Old-style类已经完全移除了.

参考资料:

 http://docs.python.org/2/library/functions.html#super
 http://docs.python.org/2/reference/datamodel.html#newstyle
 http://stackoverflow.com/questions/9698614/super-raises-typeerror-must-be-type-not-classobj-for-new-style-class
 http://stackoverflow.com/questions/9699591/instance-is-an-object-but-class-is-not-a-subclass-of-object-how-is-this-po/9699961#9699961

