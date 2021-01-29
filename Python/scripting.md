### Python脚本编程概述

#### Python解释器

1. Python和Bash的区别

   1. Python是脚本语言，而Bash是用于输入和执行命令的Shell程序

   

### 调试和分析Python脚本程序

1. Python拥有很多调试工具

   1. windpdb
   2. pydev
   3. pydb
   4. pdb
   5. gdb
   6. pydebug

2. 在解释器中使用pdb

   1. 首先导入pdb模块和要调试的脚本名称（这里是pdb_example）

      `import pdb_example`

      `import pdb`

   2. 使用run函数，并传入字符串

      `pdb.run('pdb_exmaple.Student(5)')

   3. 在使用pdb时，按两下Tab可以知道可用的命令

3. 在命令行中运行pdb

   1. `python -m pdb pdb_example.py`

4. 在脚本中使用

   1. `import pdb`
   2. 然后使用set_trace()等函数

5. 调试基本程序崩溃的方法

   1. 在命令行使用trace --trace就可以逐行跟踪程序：`python -m trace --trace trace_example.py`

6. 分析程序并计时

   1. 使用Python的`cProfile`模块
   2. `python3 -m cProfile cprof_example.py`
   3. 会输出所有被调用函数所花费的时间：
      1. `ncalls`：调用次数
      2. `tottime`：该函数花费的时间
      3. `percall`：该函数单次调用花费的平均时间
      4. `cumtime`该函数和所有子函数花费的平均时间
      5. `percall`：该函数单次调用包括其子函数花费的平均时间
      6. `filename`每个函数调用的相关信息
   4. `timeit`模块

### 单元测试框架简介

1. `unittest`是Python中的单元测试框架，支持多种任务：
   1. 测试夹具：执行一个或多个测试用例的准备和清理工具
   2. 测试用例：单独的测试用例
   3. 将测试用例聚合到测试套件中：用于一次执行多个测试用例
   4. 运行测试：执行测试用例，并向用户输出信息
2. 使用unittest
   1. 导入`unittest`模块，使用其中的`TestCase`基类
   
   2. 示例
   
      ```python
      def add_numbers(x, y):
          return x + y
      
      import arithmetic
      import unittest
      
      class Test_addition(unittest.TestCase):
          //测试整数
          def test_add_numbers_int(self):
              sum = arithmetic.add_numbers(50, 50)
              self.assertEqual(sum, 100)
              
          //测试浮点数
          def test_add_numbers_float(self):
              sum = arithmetic.add_numbers(50.50, 50)
              self.assertEqual(sum, 100.50)
              
          //测试字符串
          def test_add_numbers_int(self):
              sum = arithmetic.add_numbers('hello', 'python')
              self.assertEqual(sum, 'hellopython')
              
      if __name__ == '__main__':
          unittest.main()
      ```
   
   3. 运行后会返回3中状态之一
   
      1. OK
      2. FAILED
      3. ERROR
   
   4. unittest中常用的方法
   
      1. `assertEqual()和assertNotEqual()`检查是否达到语气结果
      2. `assertTrue()和assertFalse()`检查一个表达式的布尔值
      3. `assertRaises()`检查是否触发了特定异常
      4. `setUp()和tearDown()`定义之前和之后执行的指令

### 自动化常规管理活动

1. 通过重定向接收输入

2. 通过管道接收输入

3. 通过文件接收输入

4. 在运行时处理密码

   1. 使用paramiko模块，他是SSH的Python实现

5. 执行外部命令并获取其输出

   1. 使用`subprocess`模块可以轻松生成新进程并获取其返回代码，也可以执行外部命令以及启动新程序

   2. 例子

      ```python
      import subprocess
      
      subprocess.call(["touch", "sample.txt"])
      subprocess.call(["ls"])
      print("Sample file created")
      
      subprocess.call(["rm", "sample.txt"])
      subprocess.call(["ls"])
      print("Sample file deleted")
      ```

      1. 相当于可以在Python脚本中使用命令行的命令

6. 使用subprocess模块捕获输出

   1. 示例

      ```python
      import subprocess
      
      res = subprocess.run(['ls', '-1'], stdout=subprocess.PIPE,)
      print('returncode:', res.returncode)
      print(' {} bytes in stdout:\n{}'.format(len(res.stdout), res.stdout.decode('utf-8')))
      ```

   2. `subprocess.run()`可以运行终端命令，包括命令行参数。

   3. 返回码表示该子进程的退出状态，如果为0，表示它已经成功运行

   4. subprocess中run和call的区别在于：

      1. call返回的只是状态码
      2. 而run返回的是CompletedProcess类对象，所以可以看到后面得到状态码还要通过访问其成员：`res.returncode`

7. 在运行时提示输入密码，并验证密码

   1. 使用`getpass`模块处理密码
   2. `p = getpass.getpass()`
      1. 提示用户输入密码
      2. 将得到的密码放在p中
      3. 还可以改变提示语句，只要传入`prompt`参数
   3. `user_name = getpass.getuser()`提示用户输入用户名

8. 读取配置文件

   1. 

### 处理文件，目录和数据

### 文件归档，加密和解密

### 文本处理和正则表达式

### 文档和报告

### 处理不同类型文件

### 网络基础--套接字编程

### 使用Python脚本处理电子邮件

### 通过Telnet和SSH远程控制主机

### 创建图形界面

### 使用Apache及其他类型的日志文件

### SOAP和RESTful API通信

### 网络爬虫--从网站中提取有用的数据

### 统计信息的收集和报告

### MySQL和SQLite数据库管理

