#八天深入理解Pyhton课程

**视频教程链接**：<http://edu.csdn.net/course/detail/2116> 

**Python版本：2.7**

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



##3、字符串处理与特殊函数
用引号括起来的字符集合称之为字符串，引号可以是一对单引号，双引号，三引号（单/双）

**备注：python中三引号可以将复杂的字符串进行复制：python三引号允许一个字符跨多行，字符串中可以包含换行符、制表符以及其他特殊字符。三引号的语法是一对连续的单引号或者双引号（通常都是成对的用）**

创建字符串很简单，只要为变量分配一个值即可。例如：

```
var1 = 'Hello World!'
var2 = “Python Programming”
var3 = '''hello chuanzhiboke'''
var4 = """hello chuanzhiboke"""
```

但是如果需要输出	Hello "dear"怎么办呢？

```
print "hello \"dear\""	#利用\的转义字符
print '''hello "dear"'''
```

###3.1.Python访问字符串中的值
Python不支持单字符类型，单字符也在Python也是座位一个字符串使用

Python访问子字符串，可以使用方括号来截取字符串---即分片操作

```
var1 = 'Hello World!'
var2 = "Python Programming"
print "var1[0]:",var1[0]
print "var2[1:5]:",var2[1:5]
以上实例执行结果：
var1[0]: H
var2[1:5]: ytho
```

###3.2.Python字符串更新
可以对已存在的字符串进行修改，并赋值给另一个变量

```
var1 = 'Hello World'
print "Updated mystr :-", var1[:6]+'Python'
Updated mystr :- Hello Python
```

###3.3.Python字符串运算符
下表实例变量a值为字符串“Hello” ，b变量值为“Python”:

操作符|描述|实例
---|---|---
+|字符串连接|a+b输出结果：HelloPython
\*|重复输出字符串|a\*2输出结果：HelloHello
[]|通过索引获取字符|a[1]输出结果e
[:]|截取字符串中的一部分|a[1:4]输出结果ell
in|成员运算符|如果字符串中包含给定的字符返回ture
not in|成员运算符|如果字符串中不包含给定的字符返回True
r/R|原始字符串|字符串直接按照字面的意思来使用，没有转移或不能打印的字符

原始字符串除在字符串的第一个引号前加上字母“r”（可以大小写）以外，与普通字符串有着几乎完全相同的语法

```
print r'hello\n'
```

###3.4.Python字符串格式化
```
Python支持格式化字符串的输出
在Python中，字符串格式化使用与C中sprintf函数一样的语法
print "My name is %s and weight is %d kg!" % ('Zara',21)
```

###3.5.字符串各种函数
以下start和end可以缺省参数

start = 0
end = len(mystr)
str = 'itcast'


```
mystr = 'hello world itcast and itcastcppp'
```

检测str是否包含在mystr中，如果是返回开始的索引值，否则返回-1

```
mystr.find(str,0,len(mystr))
```

跟find()方法一样，只不过如果str不在mystr中会报一个异常

```
mystr.index(str,0,len(mystr))
```

返回str在start和end之间在mystr里面出现的次数

```
mystr.count(str,0,len(mystr))
```

以encoding指定的编码格式解码mystr，如果出错默认报一个ValueError的异常，除非errors指定的是'ignore'或者‘replace’

```
mystr.decode(encoding='UTF-8',errors='strict')
```

把mystr中的str1替换成str2，如果count指定，则替换不超过count次

```
mystr.replace(str1,str2,mystr.count(str1))
```

以str为分隔符切片mystr，如果maxsplit有指定值，则仅分隔maxsplit个子字符串

```
mystr.split(str=" ",2)
```

把字符串的第一个字符大写

```
mystr.capitalize()
```

返回一个原字符串居中，并使用空格填充至长度width的新字符串

```
mystr.center(width)
```

检查字符串(start,end)是否以obj结束，如果是返回True，否则返回False

```
mystr.endswith(obj,0,len(mystr))
```

检查字符串(start,end)是否以obj开头，如果是返回True，否则返回False

```
mystr.startswith(obj)
```

把字符串中的tab符号转为空格，默认的空格数tabsize是8

```
mystr.expandtabs(tabsize=8)
```

如果mystr至少有一个数字并且所有字符都是字母或数字则返回True，否则返回False

```
mystr.isalnum()
```

如果mystr至少有一个字符并且所有字符都是字母（不包含数字和空格）则返回True，否则返回False

```
mystr.isalpha()
```

如果mystr只包含数字则返回True否则返回False

```
mystr.isdigit()
```

如果mystr中包含至少一个区分大小写的字符，并且所有这些（区分大小写的）字符都是小写，则返回True，否则返回False

```
mystr.islower()
```

如果mystr中包含至少一个区分大小写的字符，并且所有这些（区分大小写的）字符都是大写，则返回True，否则返回False

```
mystr.isupper()
```

如果mystr中只包含空格，则返回True，否则返回False

```
mystr.isspace()
```

如果mystr是标题化的（见title()）则返回True，否则返回False

```
mystr.istitle()
```

转换mystr中所有大写字母为小写

```
mystr.lower()
```

转换mystr中所有小写字母为大写

```
mystr.upper()
```

mystr中每个字符后面插入str，构造出一个新的字符串

```
mystr.join(str)
```

返回一个原字符串左对齐，并使用空格填充至长度width的新字符串

```
mystr.ljust(width)
```

返回一个原字符串右对齐，并使用空格填充至长度width的新字符串

```
mystr.rjust(width)
```

截掉mystr左边的空格

```
mystr.lstrip()
```

截掉mystr字符串末尾的空格

```
mystr.rstrip()
```

类似find()函数，不过是从右边开始查找

```
mystr.rfind(str,start=0,end=len(mystr))
```

类似index(),不过是从右边开始

```
mystr.rindex(str,start=0,end=len(mystr))
```

把mystr以str分割成三部分，str前，str和str后

```
mystr.partition(str)
```

类似于partion()函数，不过是从右边开始，

```
mystr.rpartition(str)
```

按照行分隔，返回一个包含各行作为元素的列表

```
mystr.splitlines()
```

返回长度为width的字符串，原字符串mystr右对齐，前面填充0

```
mystr.zfill(width)
```

检查字符串是否包含十进制字符，这种方法只存在于unicode对象

```
mystr.isdecimal()
```

###3.6.函数高级

####3.6.1.定义一个函数
可以定义一个由自己想要功能的函数，以下是简单的规则：

```
语法

def functionname( parameters ):
	"函数_文档字符串"
	function_suite
	return [expression]
默认情况下，参数值和参数名称是按函数声明中定义的顺序匹配起来的

实例

以下为一个简单的Python函数，它将一个字符串座位传入参数，再打印到标准显示设备
def printme( str ):
	"打印传入的字符串到标准显示设备上"
	print str
	return
```

####3.6.2.函数调用
```
#coding=utf-8
# Function definition is here
def printme( str ):
	"打印任何传入的字符串"
	print str;
	return;
# Now you can call printme function
printme("我要调用用户自定义函数！");
printme("再次调用同一函数");

以上实例输出结果：
我要调用用户自定义函数！
再次调用同一函数	
```

####3.6.3.按值传递参数和按引用传递参数

```
1、按值传递，单个变量
2、按引用传递
如果你在函数里修改了参数，那么在调用这个函数的函数里，原始的参数也被改变了，例如：

#可写函数书名
def changeme( mylist ):
	"修改传入的列表"
	mylist.append([1,2,3,4]);
	print "函数内取值：",mylist
	return

#调用changeme函数
mylist = [10,20,30];
changeme( mylist );
print "函数外取值：",mylist
传入函数的和在末尾添加新内容的对象用的是同一个引用。故输出结果如下：

函数内取值： [10, 20, 30, [1, 2, 3, 4]]
函数外取值： [10, 20, 30, [1, 2, 3, 4]]
```

####3.6.4.参数
以下是调用函数时可使用的正式参数类型：

```
以下是调用函数时可使用的正式参数类型：

必备参数
命名参数
缺省参数
不定长参数

必备参数
	必备参数需以正确的顺序传入函数。调用时的数量必须和声明时的一样。
	调用printme()函数，你必须传入一个参数，不然会出现语法错误
	
命名参数
	命名参数和函数调用关系紧密，调用方用参数的命名确定传入的参数值。
	因为Python解释器能够用参数名匹配参数值。用命名参数调用printme()函数：
	def printme(str,name):
		"打印任何传入的字符串"
		print str;
		print name;
		return;
	#调用printme函数
	printme(name="test",str="My string");
	
缺省参数
	调用函数时，缺省参数的值如果没有传入，则被认为是默认值。下例会打印默认的age
	
	#可写函数说明
	def printinfo(name,age=35):
		"打印任何传入的字符串"
		print "Name:",name;
		print "Age",age;
		return;
		
	#调用printinfo函数
	printinfo(age=50,name="miki");
	printinfo(name="miki");
	
	以上实例输出结果：
	Name: miki
	Age 50
	Name: miki
	Age 35

不定长参数
	你可能需要一个函数能够处理比当初声明时更多的参数。这些参数叫做不定长参数
	基本语法如下：
	def functionname([formal_args,] *var_args_tuple):
		"函数_文档字符串"
		function_suite
		return [expression]
	加了星号(*)的变量名会存放所有未命名的变量参数。选择不多传参数也可，如下实例：
	
	#可写函数说明
	def printinfo(arg1,*vartuple):
		"打印任何传入的参数"
		print "输出："
		print arg1
		for var in vartuple:
			print var
		return;
		
	#调用printinfo函数
	printinfo(10);
	printinfo(70,60,50);
	以上实例输出结果：
	
	输出：
	10
	输出：
	70
	60
	50
```

####3.6.5.匿名函数
```
用lambda关键词能创建小型匿名函数。这种函数得名于省略了用def声明函数的标准步骤。

lambda函数的语法只包含一个语句，如下：

lambda[arg1[,arg2,...argn]]:expression
如下实例：
#可写函数说明
sum = lambda arg1,arg2:arg1+arg2;

#调用sum函数
print "Value of total:",sum(10,20)
print "Value of total:",sum(20,20)
以上实例输出结果：

Value of total: 30
Value of total: 40
```

#####3.6.6.return语句
```
return 语句[表达式]退出函数，选择性地像调用方返回一个表达式。
def sum(arg1,arg2):
	#返回两个参数的和“
	total = arg1 + arg2
	print "Inside the function :",total
	return total;
	
#调用函数
total = sum (10,20);
print "Outside the function :",total

以上实例输出结果:
Inside the function : 30
Outside the function : 30
```

####3.6.7.变量作用域
	一个程序的所有的变量并不是在哪个位置都可以访问的。访问权限决定于这个变量是在哪里赋值的。

	变量的作用域决定了在哪一部分程序你可以访问哪个特定的变量名称。两种最基本的变量作用域如下
	
	全局变量
	
	局部变量
	

##4.面向对象
###4.1面向对象技术简介
	类(Class):用来描述具有相同的属相和方法的对象的集合。它定义了该集合中每个对象所共有的属性和方法。对象是类的实例。
	对象：通过类定义的数据结构实例。对象包括两个数据成员（类变量和实例变量）和方法
	实例化：创建一个类的实例，类的具体对象。
	
	方法：类中定义的函数
	数据成员：类变量或者实例变量用于处理类及其实例对象的相关的数据
	方法重载：如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），重载。
	实例变量：定义在方法中的变量，只作用于当前实例的类
	类变量：类变量在整个实例化的对象中是公用的。类变量定义在类中且在函数体之外。类变量通常不作为实例变量使用。
	继承：即一个派生类（derived class）继承基类（base class）的字段和方法。
		继承也允许把一个派生类的对象座位一个基类对象对待
		例如，有这样一个设计：一个Dog类型的对象派生自Animal类，这是模拟”是一个（is-a）“关系（例如，Dog是一个Animal）
		
###4.2.创建类
	使用class语句来创建一个新类，class之后为类的名称并以冒号结束，如下实例：
	类的属性包括成员变量和方法，其中方法的定义和普通函数的定义非常类似，但方法必须以self作为第一个参数
	可以直接在类外通过对象名访问，如果想定义成私有的，则需在前面加2个下划线'__'
	构造方法__init__()方法是一种特殊的方法，被称为类的构造函数或初始化方法，当创建了这个类的实例时就会调用该方法。
	构造方法支持重载，如果用户自己没有重新定义构造方法，系统就自动执行默认的构造方法
	析构方法__del__(self)在释放对象时调用，支持重载，可以在里面进行一些释放资源的操作，不需要显示调用
	
	class ClassName
		'类的帮助信息'		#类文档字符串
		类变量				#类体 class_suite 由类成员，方法，数据属性组成
		def __init__(self,paramers):
		def 函数(self,...)
		......
		
	举例：
	>>>class Employee:
		classSpec = "it is a test class"
		def __init__(self,name,salary):
			self.name = name
			self.salary=salary			
		def hello(self):
			print "name="+name
			
	在Python类中定义的方法通常有三种：实例方法，类方法以及静态方法。
	这三者之间的区别是实例方法一般都以self作为第一个参数，必须和具体的对象实例进行绑定才能访问
	而类方法以cls作为第一个参数，cls表示类本身，定义时使用@classmethod,那么通过cls引用的必定是类对象的属性和方法
	
	而静态方法不需要默认的任何参数，跟一般的普通函数类似，定义的时候使用@staticmethod
	静态方法中不需要额外定义参数，因此在静态方法中引用类属性的话，必须通过类对象来引用
	
	而实例方法的第一个参数是实例对象self，那么通过self引用的可能是类属性、也有可能是实例属性（这个需要具体分析），不过在存在同名名称的类属性和实例属性的情况下，实例属性优先级更高
	
###4.3.创建实例对象
	要创建一个类的实例，你可以使用类的名称，并通过__init__方法接收参数 
	
	”创建 Employee 类的第一个对象“
	emp1 = Employee('Zara',2000)
	”创建 Employee 类的第二个对象“
	emp2 = Employee('Manni',5000)	
	
	
####4.4.访问属性
	使用点(.)来访问对象的属性，使用如下类的名称访问类变量：
	emp1.displayEmployee()
	
	你可以添加，删除，修改类的属性，如下显示：
	emp1.age = 7		#添加一个 'age' 属性
	emp1.age = 8		#修改 ‘age’ 属性
	del emp1.age		#删除 ‘age’ 属性
	
	getattr(obj,name[,default]):访问对象的属性
	hasattr(obj,name):检查是否存在一个属性
	setattr(obj,name,value):设置一个属性。如果属性不存在，会创建一个新属性
	delattr(obj,name)：删除属性
	
####4.5.Python内置类属性
	__dict__:类的属性（包括一个字典，由类的数据属性组成）
	__doc__:类的文档字符串
	__name__:类名
	__module__:类定义所在的模块（类的全名是'__main__classname',如果类位于一个导入模块mymod中，那么className.__modul__等于mymod）
	__base__:类的所有父类构成元素(包含了一个由父类组成的元素)
	
	
####4.6.Python对象销毁（垃圾回收）
	在Python内部记录着使用中的对象各有多少引用
	一个内部跟踪变量，称为一个引用计数器
	
	当对象呗创建时，就创建了一个引用计数，当这个对象不再需要时，也就是说，这个对象的引用计数变为0时，它被垃圾回收。但是回收不是”立即“的，由解释器在适当的时机，将垃圾对象占用的内存空间回收
	
####4.7.类的继承
	面向对象的编程带来的主要好处之一是代码的重用，实现这种重用的方法之一是通过继承机制。
	继承完全可以理解成类之间的类型和子类型关系
	
	继承语法：
	class 派生类名（基类名）：//...基类名写在括号里，基本类是在类定义的时候，在元组中指明的。
	
	在python继承中的一些特点：
	
	1：在继承中基类的构造（__init__()方法）不会被自动调用，它需要在派生类的构造中亲自专门调用。
	2：在调用基类的方法时，需要加上基类的类名前缀，且需要带上self参数变量
		区别于在类中调用普通函数时并不需要带上self参数
	3：Python总是首先查找对应类型的方法，如果它不能再派生类中找到对应的方法，它才开始到基类中逐个查找
		（先在本类中查找调用的方法，找不到采取基类中找）
	如果在继承元组中列了一个以上的类，那么它就被称作”多重继承“
	
	语法：
	派生类的声明，与他们的父类类似，继承的基类列表跟在类名之后，如下所示：
	
	class SubClassName (ParentClass1[,ParentClass2,...]):
		'Optional class documentation string'
		class_suite
		
	实例：
	#coding=utf-8
	#!/usr/bin/python
	
	class Parent:		#定义父类
		parentAttr = 100
		def __init__(selt):
			print "调用父类构造函数"
			
		def parentMethod(self):
			print '调用父类方法'
			
		def setAttr(self,attr):
			Parent.parentAttr = attr
			
		def getAttr(selt):
			print "父类属性：",Parent.parentAttr
	
	class Child(Parent):	#定义子类
		def __init__(self):
			print "调用子类构造方法"
			
		def childMethod(self):
			print '调用子类方法 child method'
			
	c = Child()		#实例化子类
	c.childMethod()	#调用子类的方法
	c.parentMethod()	#调用父类方法
	c.setAttr(200)	#再次调用父类的方法
	c.getAttr()		#再次调用父类的方法	
	
	以上代码执行结果如下：
	调用子类构造方法
	调用子类方法 child method
	调用父类方法
	父类属性： 200
	
	你可以继承多个类
	
	class A:		#定义类A
	......
	
	class B:		#定义类B
	......
	
	class C(A,B)	#继承类A和类B
	......
	
	你可以使用issubclass()或者isinstance()方法来检测
	
	issubclass()	-	布尔函数，判断一个类是另一个类的子类或者子孙类，语法：issubclass(sub,sup)
	isinstance(obj,Class) 布尔函数，如果obj是Class类的实例对象或者是Class子类的实例对象则返回true
	方法重写
	如果你的父类方法的功能不能满足你的需求，你可以在子类重写你父类的方法:
	
	实例：
	
	#coding=utf-8
	#!/usr/bin/python
	
	class Parent:		#定义父类
		def myMethod(self):
			print '调用父类方法'
			
	class Child(Parent):	#定义子类
		def myMethod(self):
			print '调用子类方法'

	c = Child()		#子类实例
	c.myMethod()		#子类调用重写方法
	
	执行以上代码输出结果如下：
	调用子类方法
	
	
	基础重载方法
	下表累出了一些通用的功能，你可以在自己的类重写：	
序号|方法|描述|简单的调用方法
---|---|---|---
1|__init__(selt[,args...])|构造函数|obj=className(args)
2|__del__(self)|析构函数，删除一个对象|del obj
3|__repr__(self)|转化为供解释器读取的形式|repr(obj)
4|__str__(self)|用于将值转化为适于人阅读的形式|str(obj)
5|__cmp__(self,x)|对象比较|cmp(obj,x)

	运算符重载
	Python同样支持运算符重载，实例如下：
	
	#!/usr/bin/python
	
	class Vector:
		def __init__(self,a,b):
			self.a = a
			self.b = b
		def __str__(self):
			return 'Vector (%d,%d)' % (self.a,self.b)
		def __add__(self,other):
			return Vector(self.a+other.a,self.b+other.b)
			
	v1 = Vector(2,10)
	v2 = Vector(5,-2)
	print v1+v2
	
	以上代码执行结果如下所示：
	Vector (7,8)
	类属性与方法
	类的私有属性
	
	__private_attrs:两个下划线开头，声明该属性为私有，不能再类的外部被使用或直接访问。在类内部的方法中使用时self.__private_attr
	
	类的方法
	在类的内部，使用def关键字可以为类定义一个方法，与一般函数定义不同，类方法必须包含参数self，且为第一个参数
	
	类的私有方法
	__private_method:两个下划线开头，声明该方法为私有方法，不能在类的外部调用。在类的内部调用self.__private_methods
	
	实例
	
	#coding=utf-8
	#!/usr/bin/python
	
	class JustCounter:
		__secretCount = 0	#私有变量
		publicCount = 0		#公开变量
		
		def count(self):
			self.__secretCount += 1
			self.publicCount += 1
			print self.__secretCount
		
	counter = JustCounter()
	counter.count()
	counter.count()
	print counter.publicCount
	print counter.__secretCount	#报错，实例不能访问私有变量
	
	Python通过改变名称来包含类名：
	
	1
	Traceback (most recent call last):
	2
	2
	File "/Users/LJaer/PycharmProjects/learnPython/test.py", line 18, in <module>
	print counter.__secretCount  # 报错，实例不能访问私有变量
	AttributeError: JustCounter instance has no attribute '__secretCount'
	
	Python不允许实例化的类访问私有数据，但你可以通过object._className__attrName访问属性，将如下代码替换以上代码的最后一行代码
	
	············
	print counter._JustCounter__secretCount
	执行以上代码，执行结果如下：
	
	1
	2
	2
	2
	
课堂例题：

```Python
#!/usr/bin/env/ python
	
class Student(object):
	'''2015 new student'''
	grade=2015
	__school="qinghua"
	
	def __init__(self,subj,name,age,sex):
		'''this is create fun'''
		self.subj=subj
		self.name=name
		self.age=age
		self.sex=sex
		print "init student"
	
	def setName(self,newname):
		self.name = newname
		
	def getName(self):
		return self.name
		
	def showStudent(self):
		print "subj=",self.subj
		print "name=",self.name
		print "age=",self.age
		print "sex=",self.sex
		print "grade=",Student.grade
		print "school=",Student.__school
		
	@classmethod
	def updategrade(cls,newgrade):
		cls.grade=newgrade
		
	@classmethod
	def showClass(cls):
		print "__name__=",cls.__name__
		print "__dict__=",cls__dict__
		print "__class__=",cls.__class__

```

##5.模块

	模块让你能够逻辑地组织你的Python代码段
	把相关的代码分配到一个模块里能让你的代码更好用，更易懂
	模块也是Python对象，具有随机的名字属性用来绑定或引用。
	简单地说，模块就是一个保存了Python代码的文件。模块能定义函数，类和变量。模块里也能包含可执行的代码
	举例：
		一个叫做aname的模块里的Python代码一般都能在一个叫aname.py的文件中找到
		
###5.1.import语句
	想使用Python源文件，只需要在另一个源文件里执行import语句，可以自动防止重复import
	形如：
		import module1,module2...
	当解释器遇到import语句，如果模块在当前的搜索路径就会被导入
	#导入模块
	import support
	#现在可以调用模块里包含的函数了
	support.print_func("Zara")
	
###5.2.From...import语句
	Python的from语句让你从模块中导入一个指定的部分到当前命名空间中。语法如下：
	from modname import name1[,name2[,......nameN]]
	例如，要导入模块fib的fibonacci函数，使用如下语句：
	
	from fib import fibonacci
	这个声明不会把整个fib模块导入到当前的命名空间中，它只会将fib里的fibonacci单个引入到执行这个声明的模块的全局符号表
	
###5.3.From...import*语句
	把一个模块的所有内容全都导入到当前的命名空间也是可以的，只需要使用如下声明：
		
	from modname import *
	这提供了一个简单的方法来导入一个模块中的所有项目。然而这个声明不该被过多的使用
	
###5.4.定位模块
	当你导入一个模块，Python解析器对模块位置的搜索顺序是：
	
	当前目录
	如果不在当前目录，Python则搜索在shell变脸PYTHONPATH下的每个目录
	如果都找不到，Python会查看默认路径。UNIX下。默认路径一般为/usr/local/lib/python/
	模块搜索路径存储在system模块的sys.path变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录
	
###5.5.PYTHONPATH变量
	作为环境变量，PYTHONPATH由装在一个列表里的许多目录组成。PYTHONPATH的语法和shell变量PATH的一样
	在windwos系统，典型的PYTHONPATH如下：
		set PYTHONPATH=c:\python20\lib
	在UNIX系统，典型的PYTHONPATH如下：
		set PYTHONPATH=/usr/local/lib/python
		
###5.6.命名空间和作用域
	变量是拥有匹配对象的名字（标识符）
	命名空间是一个包含了变量名称们（键）和他们各自相应的对象们（值）的字典
	一个Python表达式可以访问局部命名空间和全局命名空间里的变量。同名隐藏的原则同C/C++
	
	每个函数都有自己的命名空间。类的方法的作用域规则和通常函数的一样
	默认任何在函数内赋值的变量都是局部的
	因此，如果要给全局变量在一个函数里赋值，必须使用global语句
	
	global VarName的表达式会告诉Python，VarName是一个全局变量，这样Python就不会在局部命名空间里寻找这个变量了。
	
	例如，我们在全局命名空间里定义一个变量money。我们再在函数内给变量money赋值，然后Python会假定money是一个局部变量
	然而，我们并没有访问前声明一个局部变money，结果就会出现一个UnboundLocalError的错误
	取消global语句的注释就能解决这个问题
	
	Money = 2000
	def AddMoney():
		#想改正代码就取消一下注释：
		#global Money	
		Money = Money +1
		
	print Money
	AddMoney()
	print Money
	
###5.6.dir()函数
	dir()函数一个排好序的字符串列表，内容是一个模块里定义过的名字
	返回的列表容纳了在一个模块里定义的所有模块，变量和函数
	
	import math
	content = dir(math)
	print content
	
	['__doc__', '__file__', '__name__', '__package__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'hypot', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'modf', 'pi', 'pow', 'radians', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'trunc']

	
###5.7.globals()和locals()函数
	根据调用地方的不同，globals()和locals()函数可被用来返回全局和局部命名空间里的名字。
	如果在函数内部调用locals()，返回的是所有能在该函数里访问的命名。
	如果在函数内部调用globals()，返回的是所有能在该函数里访问的全局名字。
	两个函数的返回类型都是字典。所以名字们能用keys()函数摘取。
	
###5.8.Python中的包
	包是一个分层次的文件目录结果，它定义了一个由模块及子包，和子包下的子包等组成的Python的应用环境
	考虑一个在Phone目录下的pots.py文件。这个文件有如下源代码：
	#pots.py #coding=utf-8 #!/usr/bin/python
	
	def Pots():
		print "I'm Pots Phone"
		
	同样地，我们有另外两个保存了不同函数的文件：
	Phone/Isdn.py	含有函数
	def Isdn():
    	print "I'm Isdn Phone"
	Phone/G3.py	含有函数G3（）
	def G3():
   		print "I'm G3 Phone"
	
	现在，在Phone目录下创建file init.py:
	Phone/__init__.py
	此文件可为空
	from Phone.Pots import Pots
	
	或者当你导入Phone时，为了能够使用所有函数，你需要在__init_.py里使用显示的导入语句，如下：
	
	from Pots import Pots
	from Isdn import Isdn
	from G3 import G3
	
	当你把这些代码添加到init.py之后，导入Phone包的时候这些类就全都是可用的了。
	
	#Now import your Phone Package
	import Phone
	
	Phone.Pots()
	Phone.Isdn()
	Phone.G3()
	以上实例输出结果：
	I'm Pots Phone
	I'm Isdn Phone
	I'm G3 Phone
	
如上，为了举例，我们只在每个文件里放置一个函数，但其实你可以放置许多函数

你也可以在这些文件里定义Python的类，然后为这些类建一个包

###5.9.模块发布
1、mymodule目录结构体如下：
	
	LJaerdeMacBook-Pro:python2.7 LJaer$ mkdir mymodule
	LJaerdeMacBook-Pro:python2.7 LJaer$ cd mymodule/
	LJaerdeMacBook-Pro:mymodule LJaer$ touch setup.py
	LJaerdeMacBook-Pro:mymodule LJaer$ mkdir suba subb
	LJaerdeMacBook-Pro:mymodule LJaer$ touch suba/aa.py suba/bb.py
	LJaerdeMacBook-Pro:mymodule LJaer$ touch subb/cc.py subb/dd.py
	LJaerdeMacBook-Pro:mymodule LJaer$ touch suba/__init__py
	LJaerdeMacBook-Pro:mymodule LJaer$ touch subb/__init__py
	LJaerdeMacBook-Pro:mymodule LJaer$ tree
	.
	|____setup.py
	|____suba
	| |______init__py
	| |____aa.py
	| |____bb.py
	|____subb
	| |______init__py
	| |____cc.py
	| |____dd.py
	
2、编写setup.py,py_modules需指明所需包含的py文件
	
	from distutils import setup

	setup(name="xwp",version="1.0",description="xwp's module",author="LJaer",py_modules=['suba.aa','suba.bb','subb.cc','subb.dd'])	


3、构建模块
	
	python setup.py build
	
	在终端执行上述语句报错
	LJaerdeMacBook-Pro:mymodule LJaer$ python setup.py build
	Traceback (most recent call last):
	  File "setup.py", line 1, in <module>
	    from distutils import setup
	ImportError: cannot import name setup
	
	将
	from distutils import setup
	改为
	from setuptools import setup
	
	再执行
	python setup.py build
	
	构建后目录结构
	.
	|____build
	| |____lib
	| | |____suba
	| | | |____aa.py
	| | | |____bb.py
	| | |____subb
	| | | |____cc.py
	| | | |____dd.py
	|____setup.py
	|____suba
	| |______init__py
	| |____aa.py
	| |____bb.py
	|____subb
	| |______init__py
	| |____cc.py
	| |____dd.py
	
4、生成发布压缩包
	
	python setup.py sdist 
	
	打包后，生成最终发布压缩包xwp-1.0.tar.gz，目录结构
	
	.
	|____build
	| |____lib
	| | |____suba
	| | | |____aa.py
	| | | |____bb.py
	| | |____subb
	| | | |____cc.py
	| | | |____dd.py
	|____dist
	| |____xwp-1.0.tar.gz
	|____setup.py
	|____suba
	| |______init__py
	| |____aa.py
	| |____bb.py
	|____subb
	| |______init__py
	| |____cc.py
	| |____dd.py
	|____xwp.egg-info
	| |____dependency_links.txt
	| |____PKG-INFO
	| |____SOURCES.txt
	| |____top_level.txt
	
5、使用压缩包
	
	将xwp-1.0.tar.gz拷贝到另外一个文件夹
	
	LJaerdeMacBook-Pro:test LJaer$ ls
	xwp-1.0.tar.gz
	LJaerdeMacBook-Pro:test LJaer$ tar zxvf xwp-1.0.tar.gz 
	x xwp-1.0/
	x xwp-1.0/PKG-INFO
	x xwp-1.0/setup.cfg
	x xwp-1.0/setup.py
	x xwp-1.0/suba/
	x xwp-1.0/suba/aa.py
	x xwp-1.0/suba/bb.py
	x xwp-1.0/subb/
	x xwp-1.0/subb/cc.py
	x xwp-1.0/subb/dd.py
	x xwp-1.0/xwp.egg-info/
	x xwp-1.0/xwp.egg-info/dependency_links.txt
	x xwp-1.0/xwp.egg-info/PKG-INFO
	x xwp-1.0/xwp.egg-info/SOURCES.txt
	x xwp-1.0/xwp.egg-info/top_level.txt
	LJaerdeMacBook-Pro:test LJaer$ ls
	xwp-1.0		xwp-1.0.tar.gz
	LJaerdeMacBook-Pro:test LJaer$ cd xwp-1.0
	LJaerdeMacBook-Pro:xwp-1.0 LJaer$ sudo python setup.py install
	
	使用如下
	import suba.aa
	suba.aa.showaa()
	
	>>> import suba.aa
	/Library/Python/2.7/site-packages/xwp-1.0-py2.7.egg/suba/__init__.py:1: UserWarning: Module suba was already imported from /Library/Python/2.7/site-packages/xwp-1.0-py2.7.egg/suba/__init__.pyc, but /Users/LJaer/Workspaces/python2.7/test/xwp-1.0 is being added to sys.path
	>>> suba.aa.showaa()
	show aa


##6.文件操作

###6.1.打印到屏幕
	
	最简单的输出方法是用print语句，你可以给他传递零个或多个用逗号隔开的表达式。或者使用占位符
	
###6.2.读取键盘输入
	Python提供了两个内置函数从标准输入读入一行文本，默认的标准输入是键盘。如下：
	raw_input() input()函数
	
	raw_input()函数
	raw_input([prompt])函数从标准输入读取一个行，并返回一个字符串（去掉结尾的换行符）：
	
	str = raw_input("Enter your input:");
	print "Received input is:",str
	这将提示你输入任意字符串，然后在屏幕上显示相同的字符串。当我输入“Hello Python！”，它的输出如下：
	
	Enter your input:Hello Python！
	Received input is: Hello Python！
	
	input函数
	
	input([prompt])函数和raw_input([prompt])函数基本可以互换
	但是input会假设你的输入是一个幼小的Python表达式，并返回运算结果。
	
	str = input("Enter your input:");
	print "Received input is : ",str
	这会产生如下的对应着输入的结果：
	
	Enter your input:[x*5 for x in range(2,10,2)]
	Received input is :  [10, 20, 30, 40]
	
###6.3.打开和关闭文件
	你可以用file对象做大部分的文件操作
	open函数
		你必须先用Python内置的open()函数打开一个文件，创建一个file对象，相关的辅助方法才可以调用它进行读写。
	file object = open(file_name[,access_mode][,buffering])
	各个参数的细节如下：
	file_name:file_name变量是一个包含了你要访问的文件名称的字符串值
	access_mode:access_mode决定了打开文件的模式：只读，写入，追加等。同C
	buffering:缓冲区的大小。（为0，就不会有寄存；为1，访问文件时会寄存行；大于1，寄存区的缓冲大小。如果取负值，寄存区的缓冲大小则为系统默认）
	
	mode：文件访问权限
	r	只读
	w	只写，如果文件不存在，则创建，如果文件存在，则截断文件
	a	追加写
	
	r+	读写方法打开文件
	w+	可读可写文件，如果文件不存在，则创建，如果文件存在，则截断文件
	a+  追加打开文件，可读可写，如果文件不存在，则创建
	
###6.4.File对象的属性
	一个文件被打开后，你有一个file对象，你可以得到有关该文件的各种信息。
	以下是和file对象相关的所有属性的列表：
	
属性|列表
---|---
file.closed|返回true如果文件已被关闭，否则返回false
file.mode|返回被打开文件的访问模式
file.name|返回文件的名称
file.softspace|如果用print输出后，必须跟一个空格符，则返回false。否则返回true

###6.5.close()方法
	File对象的close()方法刷新缓冲区里任何还没写入的信息，并关闭该文件，这之后便不能再进行写入
	当一个文件对象的引用被重新指定给另一个文件时，Python会关闭之前的文件。用close()方法关闭文件是一个很好地习惯
	
	fileObject.close();
	
###6.6.write()方法
	Write()方法可将任何字符串写入一个打开的文件。需要重点注意的是，Python字符串可以使二进制数据，而不是仅仅是文字。
	Write()方法不在字符串的结尾不添加换行符('\n');
	语法：
	fileObject.write(string);
	
###6.7.read()方法
	read()方法从一个打开的文件中读取一个字符串。需要重点注意的是，Python字符串可以使二进制数据，而不是仅仅是文字。
	语法：
	fileObject.read([count])
	//返回的为读取数据的引用
	
	print file.read(10);
	
###6.8.seek()方法
	文件位置：
	Tell()方法告诉你文件内的当前位置，换句话说，下一次的读写会发生在文件开头这么多字节之后：
	seek(offset[,from])方法改变当前文件的位置。Offset变量表示要移动的字节数
	From变量指定开始移动字节的参考位置。如果from被设置为0，这意味着将文件的开头作为移动字节的参考位置。如果设置为1，则使用当前的位置作为参考位置。如果它被设为2，那么文件的末尾将作为参考位置
	
	#查找当前位置
	position = fo.tell();
	print "Current file position: ",position
	
	#把指针再次重新定位到文件开头
	position = fo.seekk(0,0);
	str = fo.read(10);
	print "Again read String is : ",str
	
	#关闭打开的文件
	fo.close()
	
###6.9.重命名和删除文件
	Python的os模块提供了帮你执行文件处理操作的方法，比如重命名和删除文件。
	要使用这个模块，你必须先导入它，然后可以调用相关的功能
	
	rename()方法
	rename()方法需要两个参数，当前的文件名和新文件名。
	语法：
		os.rename(current_file_name,new_file_name)	
	remove()方法
	你可以用remove()方法删除文件，需要提供要删除的文件名作为参数
	语法：
		os.remove(file_name)

	
	


	
	
	
	
	
	
	