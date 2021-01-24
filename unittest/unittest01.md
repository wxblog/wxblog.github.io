### 前言

本篇文章就来说一说python的内置单元测试库：unittest库。

----
> 回顾单元测试的定义

单元测试是指在计算机编程中，针对程序模块来进行正确性检查的测试工作。单元测试具有以下特点：

- 程序单元是应用最小的可测试部件，通常基于类或者类的方法去进行测试。
- 程序单元和其他单元式互相独立的。
- 单元测试的执行速度极快。
- 单元测试发现问题，更容易定位。
- 单元测试通常由开发人员来完成。
- 通过了解代码的实现逻辑进行测试，通常称之为白盒测试。

----

### unittest单元测试框架

python语言中有很多的单元测试框架和工具，比如pytest和nosetest。但是unittest单元测试框架作为python的标准库，是其他单元测试框架的基础，一般python中都会自带该模块。对于自动化测试来说，掌握unittest单元测试就显得尤为重要。

### unittest的定义

unittest被称为python版本的junit，主要用于python程序的单元测试。

unittest拥有支持自动化测试，测试用例之间共享`setUp(测试前置)`和`shutDown(测试后置)`代码块，集合所有的测试用例并将测试结果独立的展示在报告框架的特性。

----

### unittest中的四个重要概念

官方文档中给出了unittest中4个非常重要的概念：

- test fixture——测试固件

  一个`test fixture`代表一个或多个测试执行前的准备工作和测试结束后的清理动作。例如：创建数据库连接、启动服务进程、测试环境清理、关闭数据库连接等。

- test case——测试用例

  一个test case就是一个最小的测试单元，也就是一个完整的测试流程。针对一组特殊的输入进行特殊的验证和响应。通过集成unittest提供的测试类`TestCase`，可以创建测试用例。

- test suite——测试套件

  一个test suite就是一组测试用例，一组测试套件或者两者组成的集合。它的作用就是把测试用例集合在一起，然后一次性执行集合中所有的测试用例。

- test runner——测试运行器

  一个test runner由执行设定的测试用例和将测试结果提供给用户的两部分功能组成。

----

### 单元测试加载方法

在单元测试中，提供了两种加载测试用例的方法：

- 直接通过`unittest.main()`方法加载单元测试的测试模块，这是一种最简单的加载方法。所有的测试用例的执行顺序都是按照方法名的字符串表示的**`ASCII码`**升序排序。
- 将所有的单元测试用例TestCase加载到测试套件Test Suite集合中，然后一次性加载所有测试对象。

### 测试用例

unitest通过TestCase来构建测试用例，并要求所有的测试类都必须继承该类，它是所有测试用例的基类。TestCase的子类会继承几个特殊方法：

- setUp()：每个测试方法运行前运行，测试前置的初始化工作。
- tearDown()：每个测试方法结束后运行，测试后的清理工作。
- setUpClass()：所有的测试方法运行前运行，单元测试类运行前的准备工作。必须使用`@classmethod`装饰器进行装饰。整个测试类运行过程中只会执行一次。
- tearDownClass()：所有的测试方法结束后运行，单元测试类运行后的清理工作。必须使用`@classmethod`装饰器进行装饰。整个测试类运行过程中只会执行一次。

先来看一个简单的示例：

```python
import unittest


class TestDemo(unittest.TestCase):

    @classmethod
    def setUpClass(cls) -> None:
        print("测试类前置方法...")

    @classmethod
    def tearDownClass(cls) -> None:
        print("测试类后置方法...")

    def setUp(self) -> None:
        print("测试方法前置方法...")

    def tearDown(self) -> None:
        print("测试方法后置方法...")

    def test_1(self):
        print("我去测试一下...")

    def test_2(self):
        print("我去测试两下...")


if __name__ == "__main__":
    unittest.main()
```

执行结果

```shell
测试类前置方法...
测试方法前置方法...
我去测试一下...
测试方法后置方法...
.测试方法前置方法...
我去测试两下...
测试方法后置方法...
.测试类后置方法...

----------------------------------------------------------------------
Ran 2 tests in 0.012s

OK
```

可以看到测试类的前置后置，分别在开始和结束分别被执行了一次，而后是测试方法的前置后置方法包裹着测试方法执行了两次。

同时可以看到里面显示了两个点`.`，代表了运行成功。同时运行失败的标识为`F`，运行错误的标识为`E`。改造一下代码来看看。

```
import unittest


class TestDemo(unittest.TestCase):

    @classmethod
    def setUpClass(cls) -> None:
        print("测试类前置方法...")

    @classmethod
    def tearDownClass(cls) -> None:
        print("测试类后置方法...")

    def setUp(self) -> None:
        print("测试方法前置方法...")

    def tearDown(self) -> None:
        print("测试方法后置方法...")

    def test_1(self):
        print("我去测试一下...")
        raise Exception('error')

    def test_2(self):
        print("我去测试两下...")
        assert 1==2

if __name__ == "__main__":
    unittest.main()
```

运行结果：

```shell
测试类前置方法...
测试方法前置方法...
我去测试一下...
测试方法后置方法...
E测试方法前置方法...
我去测试两下...
测试方法后置方法...
F测试类后置方法...

======================================================================
ERROR: test_1 (__main__.TestDemo)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "c:/Users/hoou/PycharmProjects/web-unittest/demo/demo.py", line 22, in test_1
    raise Exception('error')
Exception: error

======================================================================
FAIL: test_2 (__main__.TestDemo)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "c:/Users/hoou/PycharmProjects/web-unittest/demo/demo.py", line 26, in test_2
    assert 1==2
AssertionError

----------------------------------------------------------------------
Ran 2 tests in 0.003s

FAILED (failures=1, errors=1)
```

可以看到运行的结果一个是错误的为E，一个是失败的为F，并且分别显示出了各自的报错信息及原由。

----

#### 测试集合

##### 加载测试类执行

```python
import unittest


class TestDemo(unittest.TestCase):

    @classmethod
    def setUpClass(cls) -> None:
        print("测试类前置方法...")

    @classmethod
    def tearDownClass(cls) -> None:
        print("测试类后置方法...")

    def setUp(self) -> None:
        print("测试方法前置方法...")

    def tearDown(self) -> None:
        print("测试方法后置方法...")

    def test_1(self):
        print("我去测试一下...")

    def test_2(self):
        print("我去测试两下...")


class Test001(unittest.TestCase):
    def test_1(self):
        print("1+1...", 2)

    def test_2(self):
        print("2+2...", 4)


if __name__ == "__main__":
    testcase1 = unittest.TestLoader().loadTestsFromTestCase(TestDemo, )
    testcase2 = unittest.TestLoader().loadTestsFromTestCase(Test001)
    suite = unittest.TestSuite([testcase1, testcase2])
    unittest.TextTestRunner(verbosity=2).run(suite)

```

执行结果：

```shell
Ran 4 tests in 0.012s

OK

C:\Users\hoou\PycharmProjects\web-unittest>c:/Users/hoou/PycharmProjects/web-unittest/venv/Scripts/python.exe c:/Users/hoou/PycharmProjects/web-unittest/demo/demo.py
测试类前置方法...
test_1 (__main__.TestDemo) ... 测试方法前置方法...
我去测试一下...
测试方法后置方法...
ok
test_2 (__main__.TestDemo) ... 测试方法前置方法...
我去测试两下...
测试方法后置方法...
ok
测试类后置方法...
test_1 (__main__.Test001) ... 1+1... 2
ok
test_2 (__main__.Test001) ... 2+2... 4
ok

----------------------------------------------------------------------
Ran 4 tests in 0.015s

OK

```

代码解释：

- TestLoader类： 测试用例加载器，返回一个测试用例集合。
- loadTestsFromTestCase类：根据给定的测试类，获取所有test开头的测试方法，返回一个测试集合。
- TestSuite类：组装所有的测试用例实例，支持添加和删除测试用例，最后传递给runner进行测试执行。

- TextTestRunner类：测试用例执行类，其中Text代表以文本的方式输出测试结果。

verbosity参数说明：

- 设置verbosity<=0的数字，表示不提示执行成功的用例数。
- 设置verbosity=1的数字，表示结果中仅以`.`表示执行成功的用例数。
- 设置verbosity>=2的数字，可以输出每个用例的执行的详细信息

----

##### 按照特定的顺序执行测试用例(加载测试方法的方式)

```python
import unittest


class Test001(unittest.TestCase):
    def test_1(self):
        print("1+1...", 2)

    def test_2(self):
        print("2+2...", 4)

    def test_3(self):
        print("3+3...", 6)


if __name__ == "__main__":
    suite = unittest.TestSuite()
    suite.addTest(Test001('test_3'))
    suite.addTest(Test001('test_2'))
    suite.addTest(Test001('test_1'))
    runner = unittest.TextTestRunner()
    runner.run(suite)

```

执行结果：

```shell
C:\Users\hoou\PycharmProjects\web-unittest>python c:/Users/hoou/PycharmProjects/web-unittest/demo/demo.py
3+3... 6
.2+2... 4
.1+1... 2
.
----------------------------------------------------------------------
Ran 3 tests in 0.001s

OK

C:\Users\hoou\PycharmProjects\web-unittest>
```

可以看到，测试用例已经按照我们设定的顺序在执行了。

----

#### 忽略某个测试方法

```python
import sys
import unittest


class Test001(unittest.TestCase):
    @unittest.expectedFailure  # 即使报错了，也会被计为成功的用例
    def test_1(self):
        print("1+1...", 2)
        assert 1 + 1 == 3

    @unittest.skip('无条件的忽略')  # 不管什么情况都会进行忽略
    def test_2(self):
        print("2+2...", 4)

    @unittest.skipIf(sys.platform == "win32", "跳过")  # 如果系统平台为window则忽略
    def test_3(self):
        print("3+3...", 6)

    @unittest.skipUnless(sys.platform == "win32", "跳过")  # 跳过该用例，除非系统平台为window
    def test_4(self):
        print("4+4...", 8)

    def test_5(self):
        print("5+5...", 10)


if __name__ == "__main__":
    unittest.main(verbosity=2)

```

执行结果：

```shell
test_1 (__main__.Test001) ... 1+1... 2
expected failure
test_2 (__main__.Test001) ... skipped '无条件的忽略'
test_3 (__main__.Test001) ... skipped '跳过'
test_4 (__main__.Test001) ... 4+4... 8
ok
test_5 (__main__.Test001) ... 5+5... 10
ok

----------------------------------------------------------------------
Ran 5 tests in 0.005s

OK (skipped=2, expected failures=1)
```

可以看到skip有两个分别是test_2和test_3，因为他们的忽略条件都成立了。

----

#### 命令行模式运行测试用例

我们要运行的代码片段：

```python
import sys
import unittest


class Test001(unittest.TestCase):
    @unittest.expectedFailure  # 即使报错了，也会被计为成功的用例
    def test_1(self):
        print("1+1...", 2)
        assert 1 + 1 == 3

    @unittest.skip('无条件的忽略')  # 不管什么情况都会进行忽略
    def test_2(self):
        print("2+2...", 4)

    @unittest.skipIf(sys.platform == "win32", "跳过")  # 如果系统平台为window则忽略
    def test_3(self):
        print("3+3...", 6)

    @unittest.skipUnless(sys.platform == "win32", "跳过")  # 跳过该用例，除非系统平台为window
    def test_4(self):
        print("4+4...", 8)

    def test_5(self):
        print("5+5...", 10)


if __name__ == "__main__":
    unittest.main(verbosity=2)

```

- 直接运行整个测试模块

  首先我们需要先cd进入我们需要运行的测试模块目录中

  执行命令：

  ```shell
  C:\Users\hoou\PycharmProjects\web-unittest\demo>python -m unittest testdemo
  1+1... 2
  xss4+4... 8
  .5+5... 10
  .
  ----------------------------------------------------------------------
  Ran 5 tests in 0.002s
  
  OK (skipped=2, expected failures=1)
  ```

  我们可以看到成功运行了。

- 执行测试模块中的某个类

  ```shell
  C:\Users\hoou\PycharmProjects\web-unittest\demo>python -m unittest testdemo.Test001
  1+1... 2
  xss4+4... 8
  .5+5... 10
  .
  ----------------------------------------------------------------------
  Ran 5 tests in 0.008s
  
  OK (skipped=2, expected failures=1)
  ```

- 执行测试模块中的某个测试方法

  ```shell
  C:\Users\hoou\PycharmProjects\web-unittest\demo>python -m unittest testdemo.Test001.test_5
  5+5... 10
  .
  ----------------------------------------------------------------------
  Ran 1 test in 0.001s
  
  OK
  
  ```

#### 批量执行测试模块

unitest单元测试框架提供的批量执行测试模块的方法，官方称之为测试发现。即可以自动发现测试用例，并执行目录下满足规则的测试模块。为了更好的发现测试用例，文件名必须以test开头，测试类和测试方法也必须以test开头。

测试发现的执行模式分为两种：

- 程序文件模式

  ```python
  import sys
  import unittest
  
  
  class Test001(unittest.TestCase):
      @unittest.expectedFailure  # 即使报错了，也会被计为成功的用例
      def test_1(self):
          print("1+1...", 2)
          assert 1 + 1 == 3
  
      @unittest.skip('无条件的忽略')  # 不管什么情况都会进行忽略
      def test_2(self):
          print("2+2...", 4)
  
      @unittest.skipIf(sys.platform == "win32", "跳过")  # 如果系统平台为window则忽略
      def test_3(self):
          print("3+3...", 6)
  
      @unittest.skipUnless(sys.platform == "win32", "跳过")  # 跳过该用例，除非系统平台为window
      def test_4(self):
          print("4+4...", 8)
  
      def test_5(self):
          print("5+5...", 10)
  
  
  if __name__ == "__main__":
      suite = unittest.TestLoader().discover('.')
      runner = unittest.TextTestRunner(verbosity=2)
      runner.run(suite)
  ```

  可以看到TestLoader类中提供了discover方法来发现测试方法，其中'.'代表了当前的文件。

- 命令行模式

  ```shell
  C:\Users\hoou\PycharmProjects\web-unittest\demo>python -m unittest discover
  1+1... 2
  xss4+4... 8
  .5+5... 10
  .
  ----------------------------------------------------------------------
  Ran 5 tests in 0.009s
  
  OK (skipped=2, expected failures=1)
  ```

  执行当前目录下的测试脚本，通过unittest提供的discover命令。

  discover命令的一些参数：

  - -v  /  --verbose：输出详细的测试信息

  - -s  /   --start-directory：开始进行搜索的目录，默认为当前目录(.)

  - -p /   --pattern：用于匹配测试文件的模式，默认为test**.py

  - -t  /  --top-level-directory：指定项目的最上层目录，通常为开始时所在的目录

  - [`-s`](https://docs.python.org/zh-cn/3.7/library/unittest.html#cmdoption-unittest-discover-s) ，[`-p`](https://docs.python.org/zh-cn/3.7/library/unittest.html#cmdoption-unittest-discover-p) 和 [`-t`](https://docs.python.org/zh-cn/3.7/library/unittest.html#cmdoption-unittest-discover-t) 选项可以按顺序作为位置参数传入。以下两条命令是等价的：

    ```
    python -m unittest discover -s project_directory -p "*_test.py"
    python -m unittest discover project_directory "*_test.py"
    ```

### 后记

unittest里面的东西还不少，就算是参考资料也写的挺累的。后续在更新断言方法和HTML test runner吧，今天先到这里吧。