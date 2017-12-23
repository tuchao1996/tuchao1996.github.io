---
layout: post
title: python常用模块程序设计
date: 2017-12-22
tags: 技术交流
---
# python访问限制 #
在`Class`内部，可以有属性和方法，而外部代码可以通过直接调用实例变量的方法来操作数据，这样，就隐藏了内部的复杂逻辑。

如果想让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，在 Python 中，实例的变量名如果以双下划线开头`__name`，就变成了一个私有变量 (private)，只有内部可以访问，外部不能访问：

举例：

	class Student(object):
	    def __init__(self, name, score):
	        self.__name = name
	        self.__score = score
	    def print_score(self):
	        print('%s: %s' % (self.__name, self.__score))

改完后，对于外部代码来说，没有什么变动，但是已经无法从外部访问到实例变量`.__name`和实例变量：

运行：

	>>> bart = Student('Bart Simpson', 98)
	>>> bart.__name
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	AttributeError: 'Student' object has no attribute '__name'

这样就确保了外部代码不能随意修改对象内部的状态，这样通过访问限制的保护，代码更加健壮。

如果外部还需要访问到这两个内部状态的话，可以给Student类增加`get_name`和`get_score`这样的方法。

如果外部还有修改需求的话，就给该类再增加`set_score`或`set_name`方法。用这样的方式去 `get set` 一个内部保护量：

	class Student(object):
	    def get_name(self):
	        return self.__name
	    def get_score(self):
	        return self.__score
	    def set_name(self, name):
	        self.__name = name
	    def set_score(self, score):
	        self.__score = score
	    # 对于set_score(self, score)我们可以借由set方法顺便做参数检查，提高代码安全性
	    def set_safe_score(self, score):
	        if score >= 0 and score <= 100:
	            self.__score = score
	        else:
	            raise ValueError('bad score')

需要注意的是，Python 中如果变量名以双下划线开头和结尾的，是特殊变量`__XXX__`。特殊变量是可以直接从类内部访问的。

有些时候，你会看到以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，**“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。**

双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问__name是因为 Python 解释器对外把__name变量改成了_Student__name，所以，仍然可以通过_Student__name来访问__name变量：

	>>> bart._Student__name
	'Bart Simpson'

但是强烈建议你不要这么干，因为不同版本的 Python 解释器可能会把__name 改成不同的变量名。

Python 的访问限制其实并不严格，主要靠自觉。

# 继承和多态 #

在 OOP 程序设计中，当我们定义一个 class 的时候，可以从某个现有的 class 继承，新的 class 称为子类（Subclass），而被继承的 class 称为基类、父类或超类（Base class、Super class）。

比如，我们已经编写了一个名为 Animal 的 class，有一个 run() 方法可以直接打印一句话，然后新建一个叫Dog的类，继承了Animal类：

	>>> class Animal(object):
	...     def run(self):
	...         print('running...')
	...
	>>> class Dog(Animal):
	...     pass
	...
	>>> little_dog = Dog()
	>>> little_dog.run()
	running...

对于 Dog 来说，Animal 就是它的父类，对于 Animal 来说，Dog 就是它的子类。

子类获得了父类的全部功能。Dog() 里继承了 run() 函数，可以给自己的实例里直接用。

那么问题来了，子类和父类如果定义的时候都有个run()，会发生什么？

	class Animal(object):
	    def run(self):
	        print('running...')
	
	class Dog(Animal):
	    def run(self):
	        print("Dog running...")
	
	class Cat(Animal):
	    def run(self):
	        print("Cat running...")
	
	# 结果如下
	Dog is running...
	Cat is running...

子类的的方法如果和父类的方法重名，子类会覆盖掉父类。因为这个特性，就获得了一个继承的好处” 多态”。

当我们定义一个 class 的时候，实际上也就是定义了一种数据类型。跟`list str dict`(python中的数据存储也是对象哦！)一个意思。使用isinstance(待判断值, 数据类型)可以做数据类型判定。

## 多态好处 ##

> 多态的好处就是，当我们需要传入 Dog、Cat、Tortoise…… 时，我们只需要接收 Animal 类型就可以了，因为 Dog、Cat、Tortoise…… 都是 Animal 类型，然后，按照 Animal 类型进行操作即可。由于 Animal 类型有 run() 方法，因此，传入的任意类型，只要是 Animal 类或者子类，就会自动调用实际类型的 run() 方法，这就是多态的意思：

更多python知识开始参考这篇博客，写的很好！

传送门：[高级python编程基础](http://yangcongchufang.com/%E9%AB%98%E7%BA%A7python%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80/python-object-class.html)

# 命令行解析模块argparse简单介绍 #

传送门：[argparse模块官方文档](https://docs.python.org/2/library/argparse.html)

> 命令行解析模块argparse，是一个用户良好型的命令行接口。
> 
> 模块定义需要的参数需求，`argparse`将计算解析系统输入`sys.args()`。
>
> 模块也能自动产生`help`和`usage messages`，当运行出错，会返回错误信息。

## 基础 ##

让我们从一个简单的例子开始，它 (几乎) 什么都不做：

	import argparse
	parser = argparse.ArgumentParser()
	parser.parse_args()

运行：

	$ python prog.py
	$ python prog.py --help
	usage: prog.py [-h]
	
	optional arguments:
	  -h, --help  show this help message and exit
	$ python prog.py --verbose
	usage: prog.py [-h]
	prog.py: error: unrecognized arguments: --verbose
	$ python prog.py foo
	usage: prog.py [-h]
	prog.py: error: unrecognized arguments: foo

结果分析：

* 不加任何参数运行，什么也不显示，没有什么用。
* 第二条展示了argparse模块的好处，几乎什么都不做，却得到了一个很有用的帮助信息。
* `--help`参数可简写成-h, 是唯一预设的 (不需要指定)。

## 定位参数 ##

案例：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("echo")
	args = parser.parse_args()
	print args.echo

运行：

	$ python prog.py
	usage: prog.py [-h] echo
	prog.py: error: the following arguments are required: echo
	$ python prog.py --help
	usage: prog.py [-h] echo
	
	positional arguments:
	  echo
	
	optional arguments:
	  -h, --help  show this help message and exit
	$ python prog.py foo
	foo

结果分析：

* 我们用到了方法`add_argument()`，用来指定程序需要接受的命令参数，本例中的echo。
* 现在运行程序必须指定一个参数。
* 方法`parse_args()`通过分析指定的参数返回一些数据，如本例中的`echo`。
* 像魔法一样，`argparse`自动生成这些变量，你可能已经注意到变量`echo`和我们指定的参数相同。

虽然现在帮助信息已经很美观了，但是还不够好。例如我们知道`echo`是个定位参数，但是却不知道该参数的意思，只能通过猜或者读源码。下面，我们可以让它更有帮助：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("echo", help="echo the string you use here")
	args = parser.parse_args()
	print args.echo

运行：

	$ python prog.py -h
	usage: prog.py [-h] echo
	
	positional arguments:
	  echo        echo the string you use here
	
	optional arguments:
	  -h, --help  show this help message and exit


再次修改：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("square", help="display a square of a given number")
	args = parser.parse_args()
	print args.square**2

运行：

	$ python prog.py 4
	Traceback (most recent call last):
	  File "prog.py", line 5, in <module>
	    print args.square**2
	TypeError: unsupported operand type(s) for ** or pow(): 'str' and 'int'

错误分析：

运行有点问题，因为如果不指定参数类型，argparse默认它是字符串。因此我们需要告诉argparse该参数是整型。

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("square", help="display a square of a given number",
	                    type=int)
	args = parser.parse_args()
	print args.square**2

修正后运行：

	$ python prog.py 4
	16
	$ python prog.py four
	usage: prog.py [-h] square
	prog.py: error: argument square: invalid int value: 'four'

## 可选参数 ##

下面让我们来看看如何添加可选参数：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("--verbosity", help="increase output verbosity")
	args = parser.parse_args()
	if args.verbosity:
	    print "verbosity turned on"

运行：

	$ python prog.py --verbosity 1
	verbosity turned on
	$ python prog.py
	$ python prog.py --help
	usage: prog.py [-h] [--verbosity VERBOSITY]
	
	optional arguments:
	  -h, --help            show this help message and exit
	  --verbosity VERBOSITY
	                        increase output verbosity
	$ python prog.py --verbosity
	usage: prog.py [-h] [--verbosity VERBOSITY]
	prog.py: error: argument --verbosity: expected one argument

结果分析：

* 当指定`--verbosity`时程序就显示一些东西，没指定的时候就不显示。
* 这个参数事实上是可选的，不指定它也不会出错。如果不指定可选的参数，对应的变量就被设置为 `None`，比如本例中的`args.verbosity`, 这就是为什么示例中的 if 没有执行的原因。
* 帮助信息发生了点变化
* 当我们使用可选参数`--verbosity`时，也必须指定一些值。

## 简写 ##

如果你很熟悉命令行，你可能已经注意到我在上面已经提到了参数的简写，非常的简单：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("-v", "--verbose", help="increase output verbosity",
	                    action="store_true")
	args = parser.parse_args()
	if args.verbose:
	    print "verbosity turned on"

运行：

	$ python prog.py -v
	verbosity turned on
	$ python prog.py --help
	usage: prog.py [-h] [-v]
	
	optional arguments:
	  -h, --help     show this help message and exit
	  -v, --verbose  increase output verbosity

## 混合使用定位参数和可选参数`--` ##

复杂一点：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("square", type=int,
	                    help="display a square of a given number")
	parser.add_argument("-v", "--verbose", action="store_true",
	                    help="increase output verbosity")
	args = parser.parse_args()
	answer = args.square**2
	if args.verbose:
	    print "the square of {} equals {}".format(args.square, answer)
	else:
	    print answer

运行：

	$ python prog.py
	usage: prog.py [-h] [-v] square
	prog.py: error: the following arguments are required: square
	$ python prog.py 4
	16
	$ python prog.py 4 --verbose
	the square of 4 equals 16
	$ python prog.py --verbose 4
	the square of 4 equals 16

* 为了让程序复杂点，我们重新加上了定位参数。
* 注意到参数的顺序是没有影响的。

来看看为程序加上处理重复参数的能力会怎么样：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("square", type=int,
	                    help="display a square of a given number")
	parser.add_argument("-v", "--verbosity", type=int,
	                    help="increase output verbosity")
	args = parser.parse_args()
	answer = args.square**2
	if args.verbosity == 2:
	    print "the square of {} equals {}".format(args.square, answer)
	elif args.verbosity == 1:
	    print "{}^2 == {}".format(args.square, answer)
	else:
	    print answer

运行：

	$ python prog.py 4
	16
	$ python prog.py 4 -v
	usage: prog.py [-h] [-v VERBOSITY] square
	prog.py: error: argument -v/--verbosity: expected one argument
	$ python prog.py 4 -v 1
	4^2 == 16
	$ python prog.py 4 -v 2
	the square of 4 equals 16
	$ python prog.py 4 -v 3
	16

除了最后一个暴露了一个 bug，其他的看起都来运行良好。让我们通过限制`--verbosity`后面跟的值来修正：

	import argparse
	parser = argparse.ArgumentParser()
	parser.add_argument("square", type=int,
	                    help="display a square of a given number")
	parser.add_argument("-v", "--verbosity", type=int, choices=[0, 1, 2],
	                    help="increase output verbosity")
	args = parser.parse_args()
	answer = args.square**2
	if args.verbosity == 2:
	    print "the square of {} equals {}".format(args.square, answer)
	elif args.verbosity == 1:
	    print "{}^2 == {}".format(args.square, answer)
	else:
	    print answer

运行：

	$ python prog.py 4 -v 3
	usage: prog.py [-h] [-v {0,1,2}] square
	prog.py: error: argument -v/--verbosity: invalid choice: 3 (choose from 0, 1, 2)
	$ python prog.py 4 -h
	usage: prog.py [-h] [-v {0,1,2}] square
	
	positional arguments:
	  square                display a square of a given number
	
	optional arguments:
	  -h, --help            show this help message and exit
	  -v {0,1,2}, --verbosity {0,1,2}
	                        increase output verbosity

## 代码案例 ##

案例如下：

命令行：`        # C:\Users\ztchao\anaconda3\envs\python27\python f:\AlphaCtrl\manage.py launch -r1 -p0
`

代码如下：

为了运行launch函数

    def __init__(self):
        parent_parser_usage = '''python manage.py <command> [<args>]'''
        parser = argparse.ArgumentParser(
                description='Run various commands',
                usage=parent_parser_usage)
        parser.add_argument('command', help='Subcommand to run')
        args = parser.parse_args(sys.argv[1:2])
		self.workspace = self.get_dir_path() + '/Data/'		
		getattr(self, args.command)()

为了运行-r1 -p0

	parser = argparse.ArgumentParser( description='Launch AlphaCtrl')
	parser.add_argument('-r', '--Repeat', dest='repeat_times', required=True, type=int )
	parser.add_argument('-p', '--Parallel', dest='flag_parallel', required=True, type=bool )
	args_dict = vars( parser.parse_args(sys.argv[2:]) )
	args_dict['workspace'] = self.workspace
	director = PlanningAgent( args_dict )

结果分析：

* 返回`args_dict={'repeat_times': 1, 'flag_parallel': True,'workspace':path}`

# multiprocessing多进程模块 #

## 基础 ##

由于 Python 是跨平台的，自然也应该提供一个跨平台的多进程支持。multiprocessing模块就是跨平台版本的多进程模块。

multiprocessing模块提供了一个Process类来代表一个进程对象，下面的例子演示了启动一个子进程并等待其结束：

from multiprocessing import Process
import os

	# 子进程要执行的代码
	def run_proc(name):
	    print 'Run child process %s (%s)...' % (name, os.getpid())
	
	if __name__=='__main__':
	    print 'Parent process %s.' % os.getpid()
	    p = Process(target=run_proc, args=('test',))
	    print 'Process will start.'
	    p.start()
	    p.join()
	    print 'Process end.'

运行：

	Parent process 928.
	Process will start.
	Run child process test (929)...
	Process end.

分析：

* 创建子进程时，只需要传入一个执行函数和函数的参数，创建一个Process实例，用start()方法启动，这样创建进程比fork()还要简单。

* join()方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。

## Pool ##

如果要启动大量的子进程，可以用进程池的方式批量创建子进程：

	from multiprocessing import Pool
	import os, time, random
	
	def long_time_task(name):
	    print 'Run task %s (%s)...' % (name, os.getpid())
	    start = time.time()
	    time.sleep(random.random() * 3)
	    end = time.time()
	    print 'Task %s runs %0.2f seconds.' % (name, (end - start))
	
	if __name__=='__main__':
	    print 'Parent process %s.' % os.getpid()
	    p = Pool()
	    for i in range(5):
	        p.apply_async(long_time_task, args=(i,))
	    print 'Waiting for all subprocesses done...'
	    p.close()
	    p.join()
	    print 'All subprocesses done.'

运行：

	Parent process 669.
	Waiting for all subprocesses done...
	Run task 0 (671)...
	Run task 1 (672)...
	Run task 2 (673)...
	Run task 3 (674)...
	Task 2 runs 0.14 seconds.
	Run task 4 (673)...
	Task 1 runs 0.27 seconds.
	Task 3 runs 0.86 seconds.
	Task 0 runs 1.41 seconds.
	Task 4 runs 1.91 seconds.
	All subprocesses done.

分析：

* 对Pool对象调用`join()`方法会等待所有子进程执行完毕，调用`join()`之前必须先调用`close()`，调用`close()`之后就不能继续添加新的Process了。

* 请注意输出的结果，task 0，1，2，3是立刻执行的，而 task 4要等待前面某个 task 完成后才执行，这是因为**Pool的默认大小在我的电脑上是 4**，因此，最多同时执行 4 个进程。这是Pool有意设计的限制，并不是操作系统的限制。如果改成：

`
	p = Pool(5)
`

就可以同时跑 5 个进程。

由于**Pool的默认大小是 CPU 的核数**，如果你不幸拥有 8 核 CPU，你要提交至少 9 个子进程才能看到上面的等待效果。
	
> PS:CPU的核心数限制进程同时运行的数量。

## 进程间通信 ##

Process之间肯定是需要通信的，操作系统提供了很多机制来实现进程间的通信。Python 的multiprocessing模块包装了底层的机制，提供了`Queue`、`Pipes`等多种方式来**交换数据**。

我们以`Queue`为例，在父进程中创建两个子进程，一个往`Queue`里写数据，一个从`Queue`里读数据：

	from multiprocessing import Process, Queue
	import os, time, random
	
	# 写数据进程执行的代码:
	def write(q):
	    for value in ['A', 'B', 'C']:
	        print 'Put %s to queue...' % value
	        q.put(value)
	        time.sleep(random.random())
	
	# 读数据进程执行的代码:
	def read(q):
	    while True:
	        value = q.get(True)
	        print 'Get %s from queue.' % value
	
	if __name__=='__main__':
	    # 父进程创建Queue，并传给各个子进程：
	    q = Queue()
	    pw = Process(target=write, args=(q,))
	    pr = Process(target=read, args=(q,))
	    # 启动子进程pw，写入:
	    pw.start()
	    # 启动子进程pr，读取:
	    pr.start()
	    # 等待pw结束:
	    pw.join()
	    # pr进程里是死循环，无法等待其结束，只能强行终止:
	    pr.terminate()

运行：

	Put A to queue...
	Get A from queue.
	Put B to queue...
	Get B from queue.
	Put C to queue...
	Get C from queue.

小结：

* 在 Unix/Linux 下，可以使用fork()调用实现多进程。

* 要实现跨平台的多进程，可以使用multiprocessing模块。

* 进程间通信是通过Queue、Pipes等实现的。

# 运行时间记录 #

python可以很方便记录程序运行的时间，`time`module。

举例：

	start_time = time.time()
	print time.strftime('%Y-%m-%d %X')+' AlphaCtrl has been launched!'
	director.run()
	print time.strftime('%Y-%m-%d %X')+' AlphaCtrl has been terminated!'
	stop_time = time.time()
	print 'Running time: ' + str( round( stop_time-start_time, 2 ) ) + ' seconds.'

运行，结果会显示`director.run()`运行所花的时间！

# 文件输出记录 #

python还可以很轻松生成报表，推荐demo如下：

	class myIO():
	    def write_fields( self, out_csv, field_list ):
	        with open( out_csv, 'wb' ) as csv_file:
	            writer = csv.DictWriter( csv_file, fieldnames = field_list )
	            writer.writeheader()
	    def write_dict_list( self, out_csv, field_list, dict_list ):
	        if len( dict_list ) == 0:
	            return
	        with open( out_csv, 'ab' ) as csv_file:
	            writer = csv.DictWriter( csv_file, fieldnames = field_list, extrasaction = 'ignore' )
	            writer.writerows( dict_list )
	    def write_csv( self, out_csv, field_list, dict_list ):
	        self.write_fields( out_csv, field_list )
	        self.write_dict_list( out_csv, field_list, dict_list )

当需要生成报表时，运行`self.io.write_csv( final_report_csv, final_report_header, self.final_report )`

* 第一个参数表示文件地址（包括文件名），第二个参数表示文件头，第三个参数表示数据（采用二维列表，行表示数据个数，列表示数据种类数）
* `myIO`是报表类；`write_fields`函数是写文件头；`write_dict_list`函数是写文件数据；`write_csv`函数是生产报表。

