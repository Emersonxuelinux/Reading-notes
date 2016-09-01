#八天深入理解Pyhton课程
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
	

	












