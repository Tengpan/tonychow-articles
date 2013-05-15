## Python中sqlite3模块使用小记

### 前记

Python的标准库中包含了对sqlite这个轻巧的数据库的支持模块，也就是sqlite3模块。sqlite数据库的好处我就不多说了，小型而强大，适合很多小型或者中型的数据库应用。最近在使用sqlite3模块遇到一些问题，解决了，顺便就记下来。

### 问题

sqlite3模块的使用很简单，如下这段测试代码，创建一个person数据表然后进行一次数据库查询操作。

    #!/usr/bin/env pypthon
    #_*_ coding: utf-8 _*_


    import sqlite3

    SCHEMA = """
             CREATE TABLE person (
                 p_id int,
                 p_name text
             )
             """

    def init():
        data = [(1, 'tony'), (1, 'jack')]
        conn = sqlite3.connect(':memory:')
        c = conn.cursor()
        try:
            c.execute(SCHEMA)
            for person in data:
                c.execute('insert into person values(?, ?)', person)
            conn.commit()
        except sqlite3.Error as e:
            print 'error!', e.args[0]
        return conn


    if __name__ =='__main__':
        conn = init()
        c = conn.cursor()
        #Do a query.
        c.execute('select * from person where p_name = ?', 'tony')
        person = c.fetchone()
        print person

运行这段代码，抛出了个异常，如下提示：

    Traceback (most recent call last):
          File "sqlite3_test.py", line 32, in <module>
              c.execute('select * from person where p_name = ?', 'tony')
              sqlite3.ProgrammingError: Incorrect number of bindings supplied. The current statement uses 1, and there are 4 supplied.

很莫名奇妙是不？明明我提供的占位符?绑定只有一个字符串参数，可是却说我提供了四个。再看仔细点，说提供了四个，正好字符串'tony'是四个字符。

### 解决

翻了翻文档，发现也给出了一个占位符查询的例子如下：

    t = (’RHAT’,)
    c.execute(’SELECT * FROM stocks WHERE symbol=?’, t)

所以将字符参数放到元组中就可以了，修改如下：

    c.execute('select * from person where p_name = ?', ('tony'))

结果依旧是抛出了同样的异常。再仔细看下，漏了个','，于是加上：

    c.execute('select * from person where p_name = ?', ('tony',))

这次终于得到最终的结果了,其中的字符为unicode类型：

    (1, u'tony')

### 原因

但是为什么？Python中的sqlite3模块提供了对sqlite数据操作的API，执行查询的函数是在sqlite3模块源码中定义的，很明显想要知道为啥，最好的方式是去看sqlite3模块的源码。我手上的Python源码是Python-2.7.4，在源码 Python-2.7.4/Modules/_sqlite/cursor.c 的函数PyObject* _pysqlite_query_execute(pysqlite_Cursor* self, int multiple, PyObject* args)中497-529行：

    ...

    /* execute() */
    if (!PyArg_ParseTuple(args, "O|O", &operation, &second_argument)) {
        goto error;
    }

    if (!PyString_Check(operation) && !PyUnicode_Check(operation)) {
        PyErr_SetString(PyExc_ValueError, "operation parameter must be str or unicode");
        goto error;
    }

    parameters_list = PyList_New(0);
    if (!parameters_list) {
        goto error;
    }

    if (second_argument == NULL) {
        second_argument = PyTuple_New(0);
        if (!second_argument) {
            goto error;
        }
    } else {
        Py_INCREF(second_argument);
    }
    if (PyList_Append(parameters_list, second_argument) != 0) {
        Py_DECREF(second_argument);
        goto error;
    }
    Py_DECREF(second_argument);

    parameters_iter = PyObject_GetIter(parameters_list);
    if (!parameters_iter) {
        goto error;
    }

    ...

从这段源码中可以看到这段代码将参数args解析成为Python的一个元组作为parameters_list，然后最这个得到的元组进行iter操作，不断地读取这个元组的元素作为参数，而Python中对一个字符串进行 parse tuple 会怎样？我觉得PyArg_ParseTuple这个函数的操作和以下代码会是类似的：
    ...
    >>> tuple('test')
    ('t', 'e', 's', 't')
    ...

所以现在我们可以看到我们的答案了，sqlite3模块中，cursor对象的execute方法会接受两个参数，第二个参数会被PyArg_ParseTuple函数转化成为Python中的tuple。而对一个字符进行tuple parse 导致的结果是将这个字符串的每个字符作为tuple的一个元素，所以上面抛出错误的时候提示的提供了四个所以错误也可以理解了。那为什么'('tony')'同样是错误呢？如下：

    >>> type(('tony'))
    <type 'str'>
    >>> type(('tony',))
    <type 'tuple'>

很明显，('tony')是一个str也就是一个字符串，相当于是'tony'，而('tony',)才是一个单元素的tuple。同样，因为：

    >>> tuple(['tony'])
    ('tony',)

所以如果那一行查询执行改为：

    c.execute('select * from person where p_name = ?', ['tony'])

同样也是可以执行成功的。


-EOF-



