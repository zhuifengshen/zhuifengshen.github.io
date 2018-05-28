---
layout:     post
title:      "Python装饰器的前世今生"
subtitle:   "\"python decorator \""
date:       2018-05-25 18:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - Python
    - Pythonic
---


## 一、史前故事
先看一个简单例子，实际可能会复杂很多：
```
def today():
	print('2018-05-25')
```
现在有一个新的需求，希望可以记录下函数的执行日志，于是在代码中添加日志代码：
```
def today():
	print('2018-05-25')
	logging.info('today is running...')
```
如果函数 yesterday()、tomorrow() 也有类似的需求，怎么做？再写一个 logging 在yesterday函数里？这样就造成大量雷同的代码，为了减少重复写代码，我们可以这样做，重新定义一个新的函数：专门处理日志 ，日志处理完之后再执行真正的业务代码
```
def logging_tool(func):
	logging.info('%s is running...' % func.__name__)
	func()

def today():
	print('2018-05-25')

logging_tool(today)
```
这样做逻辑上是没问题的，功能是实现了，但是我们调用的时候不再是调用真正的业务逻辑today函数，而是换成了logging_tool函数，这就破坏了原有的代码结构，为了支持日志功能，原有代码需要大幅修改，那么有没有更好的方式的呢？当然有，答案就是装饰器。

## 二、开天辟地
一个简单的装饰器
```
def logging_tool(func):
	def wrapper(*arg, **kwargs):
		logging.info('%s is running...' % func.__name__)
		func()  # 把today当作参数传递进来，执行func()就相当于执行today()
	return wrapper

def today():
	print('2018-05-25')

today = logging_tool(today)  # 因为装饰器logging_tool(today)返回函数对象wrapper，故这条语句相当于today=wrapper
today()  # 执行today()就相当于执行wrapper()
```
以上也是装饰器的原理！！！

## 三、Pythonic世界的初探
@语法糖
接触 Python 有一段时间的话，对 @ 符号一定不陌生了，没错 @ 符号就是装饰器的语法糖，它放在函数开始定义的地方，这样就可以省略最后一步再次赋值的操作
```
def logging_tool(func):
	def wrapper(*arg, **kwargs):
		logging.info('%s is running...' % func.__name__)
	    func()  # 把today当作参数传递进来，执行func()就相当于执行today()
	return wrapper

@logging_tool
def today():
	print('2018-05-25')

today()
```
有了 @ ，我们就可以省去today = logging_tool(today)这一句了，直接调用 today() 即可得到想要的结果。
**不需要对today() 函数做任何修改，只需在定义的地方加上装饰器**，调用的时候还是和以前一样。
**如果我们有其他的类似函数，可以继续调用装饰器来修饰函数**，而不用重复修改函数或者增加新的封装。这样，提高程序可重复利用性，并增加程序的可读性。

装饰器在 Python 使用之所以如此方便，归因于**Python函数能像普通的对象一样能作为参数传递给其他函数，可以被赋值给其他变量，可以作为返回值，可以被定义在另外一个函数内。**

**装饰器本质**上是一个Python函数或类，它可以让其他函数或类在不需要做任何代码修改的前提下增加额外的功能，装饰器的返回值也是一个函数/类对象。
它经常用于有切面需求的场景，比如：插入日志、性能测试、事务处理、缓存、权限校验等场景，装饰器是解决这类问题的绝佳设计。
有了装饰器，我们就可以抽离出大量与函数功能本身无关的代码到装饰器中并继续重用。
简单来说：装饰器的作用就是**让已经存在的对象添加额外的功能。**

## 四、多元化百家争鸣

### 1、带参数的装饰器
装饰器的语法允许我们在调用时，提供其它参数，比如@decorator(condition)。为装饰器的编写和使用提供了更大的灵活性。比如，我们可以在装饰器中指定日志的等级，因为不同业务函数可能需要不同的日志级别。
```
def logging_tool(level):
	def decorator(func):
		def wrapper(*arg, **kwargs):
			if level == 'error':
				logging.error('%s is running...' % func.__name__)
			elif level == 'warn':
				logging.warn('%s is running...' % func.__name__)
			else:
				logging.info('%s is running...' % func.__name__)
			func()
		return wrapper
	return decorator

@logging_tool(level='warn')
def today(name='devin'):
	print('Hello, %s! Today is 208-05-25' % name)

today()
```

### 2、让装饰器同时支持带参数或不带参数
```
def new_logging_tool(obj):
	if isinstanc(obj, str):  # 带参数的情况，参数类型为str
		def decorator(func):
			@functools.wraps(func)
			def wrapper(*arg, **kwargs):
				if obj == 'error':
					logging.error('%s is running...' % func.__name__)
				elif obj == 'warn':
					logging.warn('%s is running...' % func.__name__)
				else:
					logging.info('%s is running...' % func.__name__)
				func()
			return wrapper
		return decorator
	else:  # 不带参数的情况，参数类型为函数类型，即被装饰的函数
		@functools.wraps(obj)
		def wrapper(*args, **kwargs):
			logging.info('%s is running...' % obj.__name__)
			obj()
		return wrapper

@new_logging_tool
def yesterday():
	print('2018-05-24')

yesterday()

@new_logging_tool('warn')
def today(name='devin'):
	print('Hello, %s! Today is 208-05-25' % name)

today()
```
如上所示，参数有两种类型，一种是字符串，另一种是可调用的函数类型。因此，通过对参数类型的判断即可实现支持带参数和不带参数的两种情况。


### 3、类装饰器
装饰器不仅可以是函数，还可以是类，相比函数装饰器，类装饰器具有灵活度大、高内聚、封装性等优点。使用类装饰器主要依靠类的__call__方法，当使用 @ 形式将装饰器附加到函数上时，就会调用此方法。

（1）示例一、被装饰函数不带参数
```
class Foo(object):
    def __init__(self, func):
        self._func = func  # 初始化装饰的函数

    def __call__(self):
        print ('class decorator runing')
        self._func()  # 调用装饰的函数
        print ('class decorator ending')

@Foo
def bar():  # 被装饰函数不带参数的情况
    print ('bar')

bar()
```

（2）示例二、被装饰函数带参数
```
class Counter:
    def __init__(self, func):
        self.func = func
        self.count = 0  # 记录函数被调用的次数

    def __call__(self, *args, **kwargs):  
        self.count += 1
        return self.func(*args, **kwargs)

@Counter
def today(name='devin'):
	print('Hello, %s! Today is 208-05-25' % name)  # 被装饰的函数带参数的情况

for i in range(10):
    today()
print(today.count)  # 10
```

（3）示例三、不依赖初始化函数，单独使用__call__函数实现（体现类装饰器灵活性大、高内聚、封装性高的特点）
实现当一些重要函数执行时，打印日志到一个文件中，同时发送一个通知给用户
```
class LogTool(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile  # 指定日志记录文件

    def __call__(self, func):  # __call__作为装饰器函数
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)  # 输出日志
            with open(self.logfile, 'a') as fw:
                fw.write(log_string + '\n')  # 保存日志
            self.notify()  # 发送通知
            return func(*args, **kwargs)
        return wrapper

    # 在类中实现通知功能的封装
    def notify(self):
        pass

@LogTool()  # 单独使用__call__函数实现时，别忘了添加括号，进行类的初始化
def bill_func():
    pass
```

进一步扩展，给LogTool创建子类，来添加email的功能：
```
class EmailTool(LogTool):
    """
    LogTool的子类，实现email通知功能，在函数调用时发送email给用户
    """
    def __init__(self, email='admin@myproject.com', *args, **kwargs):
        self.email = email
        super(EmailTool, self).__init__(*args, **kwargs)

	# 覆盖父类的通知功能，实现发送一封email到self.email
    def notify(self):
        pass

@EmailTool()
def bill_func():
	pass
```

### 4、装饰函数 -> 装饰类
（1）函数层面的装饰器很常见，以一个函数作为输入，返回一个新的函数；
（2）类层面的装饰其实也类似，已一个类作为输入，返回一个新的类；

例如：给一个已有的类添加长度属性和getter、setter方法
```
def Length(cls):
    class NewClass(cls):
        @property
        def length(self):
            if hasattr(self, '__len__'):
	            self._length = len(self)
            return self._length
        
        @length.setter
        def length(self, value):
	         self._length = value
    return NewClass

@Length
class Tool(object):
    pass

t = Tool()
t.length = 10
print(t.length)  # 10
```

## 五、上古神器
### 1、@property -> getter/setter方法

示例：给一个Student添加score属性的getter、setter方法
```
class Student(object):
   @property
   def score(self):
      return self._score

   @score.setter
   def score(self, value):
      if not isinstance(value, int):
         raise ValueError('score must be an integer')
      if value < 0 or value > 100:
         raise ValueError('score must between 0~100!')
      self._score = value

s = Student()
s.core = 60
print('s.score = ', s.score)
s.score = 999  # ValueError: score must between 0~100!
```

### 2、@classmethod、@staticmethod
（1）@classmethod 类方法：定义备选构造器，第一个参数是类本身（参数名不限制，一般用cls）
（2）@staticmethod 静态方法：跟类关系紧密的函数

简单原理示例：
```
class A(object):
	@classmethod
	def method(cls):
        pass
```
等价于
```
class A(object):
	def method(cls):
        pass
        method = classmethod(method)
```


### 3、@functools.wraps
装饰器极大地复用了代码，但它有一个缺点：因为返回的是嵌套的函数对象wrapper，不是原函数，导致原函数的元信息丢失，比如函数的docstring、__name__、参数列表等信息。不过呢，办法总比困难多，我们可以通过@functools.wraps将原函数的元信息拷贝到装饰器里面的func函数中，使得装饰器里面的func和原函数有一样的元信息。
```
def timethis(func):
      """
      Decorator that reports the execution time.
      """
     @wraps(func)
     def wrapper(*args, **kwargs):
           start = time.time()
           result = func(*args, **kwargs)
           print(func.__name__, time.time() - start)
           return result
     return wrapper


@timethis
def countdown(n: int):
      """Counts down"""
      while n > 0:
           n -= 1


countdown(10000000)  # 1.3556335
print(countdown.__name__, ' doc: ', countdown.__doc__, ' annotations: ', countdown.__annotations__)
```

@functools.wraps让我们可以通过属性__wrapped__直接访问被装饰的函数，同时让被装饰函数正确暴露底层的参数签名信息
```
countdown.__wrapped__(1000)  # 访问被装饰的函数
print(inspect.signature(countdown))  # 输出被装饰函数的签名信息
```

### 4、Easter egg
（1） 定义一个接受参数的包装器
```
@decorator(x, y, z)
def func(a, b):
      pass
``` 
等价于
```
func = decorator(x, y, z)(func)
```
即：decorator(x, y, z)的返回结果必须是一个可调用的对象，它接受一个函数作为参数并包装它。

（2）一个函数可以同时定义多个装饰器，比如：
```
@a
@b
@c
def f():
	pass
```
等价于
```
f = a(b(c(f)))
```
即：它的执行顺序是从里到外，最先调用最里层，最后调用最外层的装饰器。


## 六、最后
对于Python装饰器，除了以上列举的示例，还有很多很多神奇的用法，同时装饰器也只是Pythonic中的冰山一角，这里仅当抛砖引玉，更多hacker用法，少年，尽情愉快地探索吧......

> 参考：
- [理解 Python 装饰器看这一篇就够了](https://foofish.net/python-decorator.html)
- [Python进阶](https://github.com/yasoob/intermediatePython)
