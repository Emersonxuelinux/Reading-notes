#八天深入理解Pyhton课程
##2、python基础数据类型

* python中的变量不需要先定义，再使用，可以直接使用，还有重新使用用以存储不同类型的值

* 变量命名遵循C命名风格

* 大小写敏感

* 变量引用计数

* del语句可以直接释放资源，变量名删除，引用计数减1

* 变量内存自动管理回收，垃圾收集

* 指定编码在文件开头加入#-- coding:UTF-8 --或者 #coding=utf-8

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

###2.1简单函数
函数定义格式

```
def add(x,y):
	z = x + y
	return z

res = add(3,5)
print res
8
```

1.def定义函数的关键字

2.x和y为形参，不需要类型修饰

3.函数定义行需跟‘:’

4.函数体整体缩进

5.函数可以拥有返回值，若无返回值，返回None，相当于C中的NULL

###2.2局部变量和全局变量
代码1.局部变量作用域覆盖全局变量

```
def p_num():
	num = 5
	print num
	
num = 10
p_num()
print num
#结果：5 10
```

代码2.函数内有局部变量定义，解释器不使用全局变量，局部变量的定义晚于被引用，报错

```
def p_num():
	print num
	num = 5
	print num
	
num = 10
p_num()
print num
#结果出错
```

代码3.函数内部可以直接访问全局变量

```
def p_num():
	print num
	
num = 10
p_num()
print num
#结果：10 10
```

代码4.函数内修改全局变量，使用global关键字

```
def p_num():
	global num
	print num
	num = 20
	print num

num = 10
p_num()
print num
```

###2.3特殊变量
```
_xxx	from module import *无法导入
__xxx__	系统定义的变量
__xxx	类的本地变量
```

###2.4表达式
####2.4.1算数表达式
```
+a			结果符号不变
-a			对结果符号取负
a + b		a加b
a - b 		a减b
a ** b		a的b次幂
a * b 		a乘以b
a / b		a除以b，真正除，浮点数保留小数
a // b		a除以b，向下取整
a %	 b		a对b取余数
```

####2.4.2逻辑表达式

表达式|描述|布尔值
------------- | -------------	| --------------
not a | a的逻辑非 | bool
a and b | a和b逻辑与 | bool
a or b | a和b逻辑或 | bool
a is b | a和b是同一个对象 | bool
a is not b | a和b不是同一个对象 | bool

####2.4.3关系表达式
运算结果为布尔类型

```
==		等于
!=		不等于
<>		不等于（废弃）
>		大于
<		小于
>=		大于等于
<=		小于等于
```

###2.5位运算
```
~a		按位取反
a<<n	a左移n位
a>>n	a右移n位
a&b		a和b按位与
a|b		a和b按位或
a^b		a和b按位异或
```
###2.6语法格式
缩进表示关系，函数，分支，循环语句后面带‘:’

###2.7分支语句
```
#if-else

if a>b:
	print("aaa")
else:
	print("bbb")
		
#if-elif-else

if a>b:
	print("a>b")
elif a==b:
	print("a==b")
else:
	print("a<b")
```

###2.8循环语句
python里的控制语句和循环语句和C中的非常相似，毕竟python是用C实现的。注意语句后面的':'不要丢掉，这一点C/C++程序员刚上手python的时候最容易犯的错误。

```
while 判断条件:
	执行语句
```
```
var = 1
while var == 1:	#该条件永远为true,循环将无限执行下去
	num = raw_input("Enter a number :")
	print "You entered: %d",num
	
	print "Good bye!"
```

Python for 循环可以遍历任何序列的项目，如一个列表或者一个字符串

```
for iterating_var in sequence:
	执行语句
```

```
for letter in 'Python':		#First Example
	print 'Current Letter :',letter

fruits = ['banana','apple','mango']
for fruit in fruits:		#Second Example
	print 'Current fruit :',fruit
```

在python中，for...else表示这样的意思，for中的语句和普通的没有区别，else中的语句会在循环正常执行完（即for不是通过break跳出而中断的）的情况下执行，while...else也是一样

```
count = 0
while count < 5:
    print (count," is less than 5")
    count = count + 1
else:
    print (count," is not less than 5")
```

以上实例输出结果为：

```
(0, ' is less than 5')
(1, ' is less than 5')
(2, ' is less than 5')
(3, ' is less than 5')
(4, ' is less than 5')
(5, ' is not less than 5')
```

###2.9.break
Python break语句，就像在C语言中，打破了最小封闭for或while循环

break语句用来终止循环语句，即循环条件没有False条件或者序列还没被完全递归完，也会停止执行循环语句

break语句用在while和for循环中。

如果您使用嵌套循环，break语句将停止执行最深层的循环，并开始执行下一行代码。

###2.10.continue
Python continue 语句跳出本次循环，而break跳出整个循环

continue语句用来告诉Python跳出当前循环的剩余语句，然后继续进行下一轮循环

continue语句用在while和for循环中

###2.11.list列表
序列都可以进行的操作包括索引，切片，加，乘，检查成员。序列中的每个元素都分配一个数字-它的位置，或索引，第一个索引是0，第二个索引是1，依次类推

列表和元组二者均能保存任意类型的python对象，索引访问元素从0开始 列表元素用[]包括，元素个数，值都可以改变 元组元素用()包括，通过切片[][:]得到子集，此操作同于字符串相关操作 切片使用的基本样式[下限:上限:步长]

####2.11.1.访问列表中的值
```
>>> aList = [1,2,3,4]
>>> aList
[1, 2, 3, 4]
>>> aList[0]
1
>>> aList[2:]
[3, 4]
>>> aList[:3]
[1, 2, 3]
```

####2.11.2.更新列表中的值
```
>>> aList[1]=5
>>> aList
[1, 5, 3, 4]
```

####2.11.3.删除列表中的值
```
>>> del aList[1]
>>> aList
[1, 3, 4]
```

####2.11.4.列表中的值出栈
```
>>> aList.pop()
4
>>> aList
[1, 3]
```

####2.11.5.列表中的值追加
```
>>> aList.append(20)
>>> aList
[1, 3, 20]
>>> aList.append('hello')
>>> aList
[1, 3, 20, 'hello']
>>> aList.append(1.23)
>>> aList
[1, 3, 20, 'hello', 1.23]
>>> aList.append(['aa','bb'])
>>> aList
[1, 3, 20, 'hello', 1.23, ['aa', 'bb']]
```

####2.11.6.Python列表脚本操作符
Python表达式|结果|描述
--------|--------|--------
len([1,2,3]) | 3 | 长度
[1,2,3]+[4,5,6] | [1,2,3,4,5,6] | 组合
['Hi!']*4 | ['Hi!','Hi!','Hi!','Hi!'] | 重复
3 in [1,2,3]	| True | 元素是否存在于列表中
for x in [1,2,3]:print x, | 1 2 3 | 迭代

####2.11.7.Python列表截取
Python的列表截取与字符串操作类型，如下所示：

```
L = ['spam','Spam','SPAM!']
操作：
```

Python表达式|结果|描述
---|---|----
L[2] | 'SPAM!' | 读取列表中的第三个元素 
L[-2] | 'Spam' | 读取列表中倒数第二个元素
L[1:] | ['Spam','SPAM!'] | 从第二个元素开始截取列表

####2.11.8.列表函数&方法
```
序号	  函数
1		cmp(list1,list2)		比较两个列表的元素
2		len(list)				列表元素个数
3 		max(list)				返回列表元素最大值
4 		min(list)				返回列表元素最小值
5 		list(seq)				将元组转换为列表

Python包含以下方法：

序号	方法
1		list.append(obj) 			在列表末尾添加新的对象
2		list.count(obj)				统计某个元素在列表中出现的次数
3		list.extend(seq)			在列表末尾一次性追加另一个序列中行的多个值（用新列表扩展原来的列表）
4		list.index(obj)				从列表中找出某个值第一个匹配项的索引位置
5		list.insert(index,obj)		将对象插入列表
6 		list.pop(obj=list[-1])		移除列表中的一个元素（默认最后一个元素），并返回该元素的值
7		list.remove(obj)			移除列表中某个值的第一个匹配项
8 		list.reverse()				反向列表中元素
9		list.sort([func])			对原列表进行排序
```


###2.12.元组Tuple
Python的元组与列表类似，不同之处在于元组的元素不能修改。也可以进行分片和连接操作。元组使用小括号，列表使用方括号。

```
>>> aTuple = ('et',77,99.9)
>>> aTuple
('et', 77, 99.9)
```

####2.12.1.访问元组
```
>>> aTuple[2]
99.9
```

####2.12.2.修改元组
```
>>> aTuple[1] = 5 #真的不能修改
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> tup2 = (1,2,3,4,5,6,7)
>>> print "tup2[1:5]:",tup2[1:5]
tup2[1:5]: (2, 3, 4, 5)
>>> tup3 = tup2 + aTuple
>>> print tup3
(1, 2, 3, 4, 5, 6, 7, 'et', 77, 99.9)
```

####2.12.3.删除元组

元组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元素

####2.12.4.元组运算符

与字符串一样，元组之间可以使用+号和*号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组

Python表达式|结果|描述
---|---|---
len([1,2,3]) | 3 | 计算元素个数
[1,2,3]+[4,5,6] | [1,2,3,4,5,6] | 连接
['Hi!']*4 | ['Hi!','Hi!','Hi!','Hi!'] | 复制
3 in [1,2,3]	| True | 元素是否存在
for x in [1,2,3]:print x, | 1 2 3 | 迭代

####2.12.5.元组索引，截取
因为元组也是一个序列，所以我们可以访问元组中的指定位置的元素，也可以截取索引中的一段元素

```
L = ['spam','Spam','SPAM!']
```

Python表达式|结果|描述
---|---|----
L[2] | 'SPAM!' | 读取列表中的第三个元素 
L[-2] | 'Spam' | 读取列表中倒数第二个元素
L[1:] | ['Spam','SPAM!'] | 从第二个元素开始截取列表

####2.12.6.无关闭分隔符
任意无符号的对象，以逗号隔开，默认为元组，如下实例：

```
print 'abc',-4.24e93,18+6.6j,'xyz'
x,y = 1,2
print "Value of x, y:", x,y

以上实例允许结果：
abc -4.24e+93 (18+6.6j) xyz
Value of x, y: 1 2
```

####2.12.7.元组内置函数
Python元组包含了以下内置函数

```
序号	  函数
1		cmp(tuple1,tuple2)		比较两个元组元素
2		len(tuple)				计算元组元素个数
3 		max(tuple)				返回元组中元素最大值
4 		min(tuple)				返回元组中元素最小值
5 		tuple(seq)				将列表转换为元组
```

####2.12.8.多维元组访问的示例
```
>>> tuple1 = [(2,3),(4,5)]
>>> tuple1[0]
(2, 3)
>>> tuple1[0][0]
2
>>> tuple1[0][2]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: tuple index out of range
>>> tuple1[0][1]
3
>>> tuple1[2][2]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
>>> tuple2 = tuple1 + [(3)]
>>> tuple2
[(2, 3), (4, 5), 3]
>>> tuple2[2]
3
>>> tuple2[2][0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object has no attribute '__getitem__'
```

###2.13.字典Dictionary
字典是python中的映射数据类型，由键值(key-value)构成，类似于关联数组或哈希表，key一般以数字和字符串居多，值则可以是任意类型的python对象，如其他容器模型。字典元素用大括号{}包括，比如：

```
>>> dict = {'Alice':'2341','Beth':'9102','Cecil':'3258'}
>>> aDict = {'host':'noname'}
```

每个键与值用冒号(:),每对用逗号分割，整体放在花括号中({})。键key必须独一无二，但值则不必。值value可以取任何数据类型，但必须是不可变的

####2.13.1.访问字典里的值
```
>>> aDict['host']
'noname'
>>> for key in aDict:
...     print key,aDict[key]
... 
host noname
>>> aDict.keys()
['host']
>>> aDict.values()
['noname']
```

####2.13.2.修改字典
向字典添加新内容的方法是增加新的键/值对，修改或删除已有键/值对

```
>>> aDict['port']=80 #如果由port key，值会被更新 否则会被新建一个port key
>>> aDict
{'host': 'noname', 'port': 80}
```

####2.13.3.删除字典元素
能删单一的元素也能清空字典，清空只需一项操作

显示删除一个字典用del命令

```
dict = {'Name':'Zara','Age':7,'Class':'First'}
del dict['Name']		#删除键是'Name'的条目
dict.clear()			#清空词典所有条目
del dict 				#删除字典
```

####2.13.4.字典键（key）的特性
字典值可以没有限制地取任何python对象，既可以是标准的对象，也可以是用户定义的，但键不行。

两个重要的点需要记住：

1、不允许同一个键出现两次。创建时如果同一个键被赋值两次，后一个值会被记住

```
>>> dict = {'Name':'Zara','Age':7,'Name':'Manni'}
>>> print "dict['Name':] ",dict['Name']
dict['Name':]  Manni
```

2、键必须不可变，所以可以用数，字符串或元组充当，所以用列表就不行

####2.13.5.字典内置函数&方法

![pythondic]	(pictures/pythondic.png)








