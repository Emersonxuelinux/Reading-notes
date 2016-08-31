#八天深入理解Pyhton课程
##2、python基础数据类型

1、python中的变量不需要先定义，再使用，可以直接使用，还有重新使用用以存储不同类型的值

2、变量命名遵循C命名风格

3、大小写敏感

4、变量引用计数

5、del语句可以直接释放资源，变量名删除，引用计数减1

6、变量内存自动管理回收，垃圾收集

7、指定编码在文件开头加入#-- coding:UTF-8 --或者 #coding=utf-8

```
>>> a=12        #无需定义，直接使用，python解释器根据右值决定左侧类型
>>> print a
12
>>> id(a)       #变量a在内存中的编号
140625747953040
>>> type(a)     
<type 'int'>	#a的类型为int类型
>>> b=12.34
>>> print b
12.34
>>> id(b)       #变量b在内存中所占内存编号
140625747961088
>>> type(b)
<type 'float'>	#b的类型为float
>>> a='itcast'	#变量a重新指向一个字符串
>>> print a
itcast
>>> id(a)		#变量a在内存中的编号为“itcast”地方，原来a所指向的内存编号里内容并没有立即释放
4463479328
>>> type(a)		#变量a现在指向一个字符串
<type 'str'>
<type 'str'>
>>> c=b
>>> print c
12.34
>>> id(c)		#变量c保存的内存中编号和b一致
140625747961088
>>> type(c)
<type 'float'>

>>> b=12
>>> id(b)		#解释器在内存中发现有保存12的这个单元，于是变量b指向了此单元，减少了存储空间的反复申请和释放
140625747953040
>>> type(b)
<type 'int'>
>>> print b
12

>>> print a
itcast
>>> del(a)
>>> print a
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'a' is not defined
>>> 
```

###简单函数
