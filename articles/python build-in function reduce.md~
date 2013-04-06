
##python内置函数reduce

####原型

reduce函数原型是reduce(function, iterable[, initializer]),返回值是一个单值.使用例子如下:

    >>> print reduce(lambda x, y: x + y, [1, 2, 3, 4, 5])
    15
    
可以看到通过传入一个函数和一个list, reduce函数返回的是这个list的元素的相加值.注意lambda函数是有两个参数的,如果我们改成一个参数会怎么样?如下:

    >>> print reduce(lambda x: x, [1, 2, 3, 4, 5])
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    TypeError: <lambda>() takes exactly 1 argument (2 given)
    
结果是抛出了错误,提示<lambda>()函数只接受一个参数缺给了两个参数.所以在reduce内部中,我们可以知道对于作为参数的function,接受了两个值作为参数的.

####深入

第一个例子中,reduce函数返回的是list变量元素的和,那reduce函数是如何实现将这个list变量元素相加起来呢?考虑到定义的匿名函数体中将x的值和y的值加起来了,所以应该和这个函数是相关的,那reduce函数给赋给这个lambda函数的两个参数分别是什么呢?
  
    >>> l = []
    >>> def fun(x, y):
    ...     l.append((x, y))
    ...     return x + y
    ... 
    >>> result = reduce(fun, [1, 2, 3, 4, 5])
    >>> result
    15
    >>> l
    [(1, 2), (3, 3), (6, 4), (10, 5)]

通过这个例子,可以看出答案已经很明显了.在reduce函数内部,对lambda函数的调用一共有四次:

    fun(1, 2)     #x = 1, y = 2,x是list的第一个元素,y是第二个元素
    fun(3, 3)     #x = 3, y = 3,x是上一次调用返回值1+2,y是第三个元素
    fun(6, 4)     #x = 6, y = 4,同上,y是第四个元素
    fun(10, 5)    #x = 10, y = 5,同上,y是第五个元素
    
最后得到reduce函数的返回值15,也就是fun函数的第四次调用的返回值.所以现在我们知道了,reduce函数对作为参数的函数是有要求的,要求这个函数接受两个参数.第一个参数的值是累积的值,而第二个参数的值是reduce函数参数中的序列的下一个元素.其实reduce函数中还有第三个可选的参数初始值,如果这个参数为空则初始值默认为序列的第一个元素,所以上面可以看到第一次调用这个函数是以序列的第一个和第二个元素作为参数的.最终,最后一次调用返回的值作为reduce函数的返回值.

####定义

reduce函数可以参考下面的定义(来自官网):
    
    def reduce(function, iterable, initializer=None):
    it = iter(iterable)
    if initializer is None:
        try:
            initializer = next(it)
        except StopIteration:
            raise TypeError('reduce() of empty sequence with no initial value')
    accum_value = initializer
    for x in it:
        accum_value = function(accum_value, x)
    return accum_value
    
reduce函数对function的调用次数为iterable参数的长度n减1.
    
参考资料:

[python build-in function reduce](http://docs.python.org/2/library/functions.html#reduce)

[python functional programming](http://www.secnetix.de/olli/Python/lambda_functions.hawk)

-EOF-
    
