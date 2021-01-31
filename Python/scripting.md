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

   1. 使用`ConfigParser`模块
   
   2. 示例
   
      ```python
      from configparser import ConfigParser
      
      p = ConfigParser()
      p.read('read_sample.ini')
      print(p.get('bug_tracker', 'url'))
      
      //可以接受多个文件
      files = ['hello.ini', 'bye.ini', 'read_sample.ini', 'welcome.ini']
      files_found = p.read(files) //这个函数可以返回读取成功的文件名的列表
      files_missing = set(files) - set(files_found)
      ```
   
9. 向脚本添加日志记录和警告代码

   1. 使用`logging`模块：跟踪程序中发生的事

   2. 使用`warnings`模块，用于警告程序员关于语言和程序库中所做的修改

   3. 示例

      ```python
      import logging
      
      LOG_FILENAME = 'log.txt'
      logging.basicConfig(filename=LOG_FILENAME, level=logging.DEBUG)
      logging.debug('This message should go to the log file')
      
      import warnings
      logging.basicConfig(level=logging.INFO,)
      warnings.warn('This warn is not sent to the logs')
      logging.captureWarnings(True)
      warnings.warn('This warn is sent to the logs')
      ```

      1. 前面一段中，输出的消息会被写入log.txt这个文件中
      2. 后面使用warnings:直接使用warn方法会打印到终端，但是如果logging中设置捕获警告，这些就会被写入日志文件

10. 限制CPU和内存的使用量

    1. 

11. 启动网页浏览器

    1. 使用`webbrowser`模块

    2. 可以使用open函数来打开浏览器

    3. 还可以在命令行使用这个模块

       1. `python3 -m webbrowser -n https://www.google.com/`
       2. 还可以用两个选项
          1. -n：打开新窗口
          2. -t：打开新标签

    4. 示例

       ```python
       import webbrowser
       
       webbrowser.open('https://baidu.com')
       ```

12. 使用os模块处理目录和文件

    1. 使用`os`模块
    2. 创建目录和删除目录还有ls
       1. `os.mkdirs()`
       2. `os.rmdir()`
       3. `os.listdir()`

13. 进行备份

    1. 使用`rsync`
    2. 

### 处理文件，目录和数据

1. 使用os模块处理目录

   1. 获取工作目录：`os.getcwd()`
   2. 更改目录：`os.chdir()`
   3. 列出文件和目录：`os.listdir()`
   4. 重命名目录：`os.rename(dir1, dir2)`
   5. 复制文件：`shutil.copy(file1, file2)`
   6. 移动文件：`shutil.move(dest1, dest2)`
   7. 重命名文件：`shutil.move(file1, file2)`
   8. 删除文件：`os.remove(file)/os.rmdir(dir)`

2. 使用路径

   1. 返回绝对路径：`os.path.absname(file)`
   2. 返回路径，不包括文件名：`os.path.dirname(path/file)`
   3. 返回文件名，不包括路径：`os.path.basename(path)`
   4. 返回路径或文件是否存在：`os.path.exists(path/file)`
   5. 返回文件大小，字节为单位：`os.path.getsize(path)`
   6. 检查输入是否是一个文件：`os.path.isfile(path)`
   7. 检查输入是否是一个目录：`os.path.isdir(path)`

3. 比较数据

   1. 比较2个csv文件示例

      ```python
      import pandas as pd
      
      df1 = pd.read_csv("s1.csv")
      df2 = pd.read_csv("s2.csv")
      s1 = set([tuple(values) for values in df1.values.tolist()])
      s2 = set([tuple(values) for values in df2.values.tolist()])
      
      s1.symmetric_difference(s2)
      
      print(pd.DataFrame(list(s1.difference(s2))), '\n')
      print(pd.DataFrame(list(s1.difference(s2))), '\n')
      ```

4. 合并数据

   1. 同样使用pandas库

5. 用模式匹配文件和目录

   1. 使用`glob`模块

6. 元数据

   1. 使用`pyPdf2`模块

7. 压缩和解压

   1. 使用`shutil`模块
   2. 使用`make_archive()`函数压缩
      1. 第一个参数：要压缩的文件
      2. 第二个参数：要压缩的格式
      3. 第三个参数：文件所在目录
   3. 使用`unpack_archive()`函数解压缩

8. 使用tarfile模块创建TAR文件

   1. 使用`tarfile`模块

   2. 创建TAR文件示例：

      ```python
      import tarfile
      
      tar_file = tarfile.open("work.tar.gz", "w:gz")
      for name in ["welcom.py", "hello.py"]:
          tar_file.add(name)
      tar_file.close()
      ```

   3. 查看TAR文件内容

      1. 示例

         ```python
         import tarfile
         
         tar_file = tarfile.open('work.tar.gz', 'r:gz')
         print(tar_file.getnames())
         ```

   4. 测试是否是有效的归档文件：`tarfile.is_tarfile()`

   5. 提取内容：`tarfile.extractall()`

   6. 读取元数据：`file.getmembers()`

### 文件归档，加密和解密

1. ZIP文件
   1. 创建：还是使用`shutil.make_archive()`函数
   2. 检查是否是有效的zip文件：`zipfile.is_zipfile()`
   3. 获取文件的元数据信息：`file.infolist()`
2. 文件加密和解密
   1. 使用`pyAesCrypt`模块
   2. 这个模块使用AES256-CBC来加密/解密文件和二进制流
   3. 加密：`pyAesCrypt.encryptStream(fin, fout, password, bufferSize)`
   4. 解密：`pyAesCrypt.decryptStream(Fin, fout, passoword, buffersize, encFilesize)`
   5. 解密中要用到的`encFileSize`是加密文件的大小，可以使用这个方法得到：`encFileSzie = stat("sample.txt.aes").st_size`

### 文本处理和正则表达式

### 文档和报告

### 处理不同类型文件

1. PDF文件

   1. 使用`PyPDF2`模块

   2. 读取pdf文件：`read_pdf = PyPDF2.PdfFileReader(pdf)`

   3. 读取pdf的页数：`read_pdf.numPages`

   4. 提取文本：

      1. `pdf_page = read_pdf.getPage(1)`
      2. `pdf_content = pdf_page.extractText()`

   5. 旋转pdf页面

      1. 要改变pdf内容时，要用`PdfFileWriter`而不是上面的`reader`

      2. 示例：

         ```python
         import PyPDF2
         
         with open('test.pdf', 'rb') as pdf:
             rd_pdf = PyPDF2.PdfFileReader(pdf)
             wr_pdf = PyPDF2.PdfFileWriter()
             
             for pg_num in range(rd_pdf.numPages):
                 pdf_page = rd_pdf.getPage(pg_num)
                 pdf_page.rotateClockwise(90)
                 wr_df.addPage(pdf_page)
                 
         	with open('rotated.pdf', wb) as pdf_out:
                 wr_pdf.wirte(pdf_out)
         ```

2. 

### 网络基础--套接字编程

1. 3种Internet模块

   1. http：超文本传输协议
   2. ftplib：执行与FTP相关的自动化工作
   3. urllib：处理与URL相关的工作的程序包

2. 套接字

   1. 使用`socket`模块

   2. 套接字是通信的接口，包括本地通信和互联网通信

   3. socket模块中有一个socket类，用于处理数据通道

   4. 创建套接字：

      1. `s = socket.socket(address_family, socket_type)`
         1. `address_family`控制OSI网络层协议
         2. `socket_type`控制OSI传输层协议
      2. `address_family`有3种：
         1. `AF_INET`：常用，用于互联网IP地址
         2. `AF_INET6`：用于IPv6地址
         3. `AF_UNIX`：用于Unix域套接字(UDS)，这是一种进程间通信协议
      3. `socket type`有2种
         1. `SOCK_DGRAM`：用于面向消息的数据报传输
         2. `SOCK_STREAM`：用于面向流的传输

   5. 示例

      ```python
      import socket
      
      host_name = socket.gethostname()
      port = 5000
      s_socket = socket.socket()
      s_socket.bind((host_name, port))
      s_socket.listen(2)
      
      conn, address = s_socket.accept()
      print("Connection from: " + str(address))
      
      while True:
          recv_data = conn.recv(1024).decode()
          if not recv_data:
              break;
          print("from connected user: " + str(recv_data))
          recv_data = input(' -> ')
          conn.send(recv_data.encode())
          
      conn.close()
      ```

3. http程序包

   1. `http.client`模块
      1. 先建立一个连接：`con_obj = http.client.HTTPConnection('Enter_URL_name', 80, timeout=20)`
         1. 这里使用的是HTTP，如果连接的是HTTPS的网站，用`HTTPSConnection`
      2. 使用get的请求方法：`con_obj.request("GET", "/")`
      3. 等待响应：`response = con_obj.getresponse()`
         1. 返回一个response对象
         2. 可以通过`response.getheaders()`来获取
      4. 使用POST请求：`con_obj.request('POST', '/post', json_data, headers_list)`
         1. `json_data`是一个字典
         2. `headers_list`是一个字典
   2. `http.server`模块
      1. 含有实现HTTP服务器的类，有2中方法：
         1. GET
         2. HEAD
      2. 
   3. `http.cookies`模块：使用cookie实现状态管理
   4. `http.cookiejar`模块：提供cookie持久性

4. 

### 使用Python脚本处理电子邮件

1. 使用`stmplib`模块

   1. 指定`host_name`：`host_name = "smtp.gmail.com"
   2. 指定端口：465
   3. 指定发送者地址：`sender = 'sender_mail_id'`
   4. 指定接受者地址：`receiver = 'receiver_mail_id'`
   5. 指定密码：
      1. 可以直接输入
      2. 也可以让用户输入：`password = getpass.getpass()`
   6. 指定消息体：`msg = 'hello from sender'`
   7. 连接服务器：`s = smptlib.SMTP_SSL(host_name, port)`
   8. 登录：`s.login(sender, password)`
   9. 发送邮件：`s.sendmail(sender, receiver, msg)`
   10. 退出：`s.quit()`

2. 使用`Email`模块添加HTML和多媒体内容

   1. 添加其他内容，使用`MIMEMultipart`：

      ```python
      text = MIMEMultipart()
      text['Subject'] = 'Test HTML Content'
      text['From'] = sender
      text['To'] = receiver
      ```

   2. 添加HTML内容，使用`MIMEText`：

      ```python
      msg = '''
      <html>
      	<body>
      		<p>Hello there, <br>
      		Good day <br>
      		<a href="http://www.baidu.com">Home</a>
      		</p>
      	</body>
      </html>
      '''
      
      html_content = MIMEText(msg, "html")
      text.attach(html_content)
      ```

   3. 最后发送的时候，第三个参数使用`text.as_string()`即可

   4. 添加附件，使用`MIMEImage`：

      ```python
      f_path = 'path_of_file'
      with open(f_path, 'rb') as f:
          img = MIMEImage(f.read())
      img.add_header('Content-Disposition', 'attachment', filename=os.path.basename(f_path))
      text.attach(img)
      ```

3. POP3和IMAP服务器

   1. 这两个是用来接收电子邮件的，Python分别为他们提供了`poplib`和`imaplib`模块

   2. POP3的意思是Post Office Protocol version 3，也就是邮局协议，可以帮助我们接受从远程服务器发送到本地计算机的电子邮件

   3. POP3的优点是：允许将电子邮件下载到本地，从而可以离线阅读下载

   4. POP3使用2个端口

      1. 110：默认不加密端口
      2. 995：加密端口

   5. 使用poplib模块接收邮件

      1. 建立连接：`email_obj = poplib.POP3_SSL(pop3_server)`

      2. 输入用户名：`email_obj.user(username)`

      3. 输入密码：`email_obj.pass_(password)`

      4. 得到消息数量

         `email_stat = email_obj.stat()`

         `NumofMsgs = emial_stat[0]`

      5. 得到邮件

         ```python
         for i in range(NumofMsgs):
             for mail in email_obj.retr(i+1)[1]:
                 print(mail)
         ```

   6. 使用imaplib模块接收邮件

      1. IMAP允许多个客户端同时访问服务器上用户的电子邮件，所以当用户需要在不同客户端访问电子邮件时，更适合使用IMAP

      2. 使用2个端口

         1. 143：默认的不加密的
         2. 993：加密的

      3. 使用方法：

         1. 建立连接和登录都一样

         2. 登录后查看收件箱：`imap_obj.select('Inbox')`

         3. 得到邮件

            ```python
            temp, data_obj = imap_obj.search(None, 'ALL')
            for data in data_obj[0].split():
                temp, data_obj = imap_obj.fetch(data, '(RFC822)')
                
            imap_obj.close()
            ```

### 通过Telnet和SSH远程控制主机

1. Telnet是一种允许用户与远程服务器通信的网络协议，经常用来远程访问和管理设备。
2. 在终端中运行Telnet命令，并给远程服务器的IP地址或主机名，即可访问远程设备
3. Telnet基于TCP，默认端口为23
4. Python的`telnetlib`模块可以在Python脚本中实现Telnet功能
5. 在使用Telnet之前，请确保正确配置它们
6. 使用`telnetlib`
7. SSH
   1. 一种网络协议，用于远程访问，管理设备
   2. SSH使用公钥加密来实现安全性
   3. Telnet与SSH的重要区别就在于SSH使用加密，也就是SSH传输的数据可以防止未经授权的实时拦截
8. subprocess模块
   1. 

### 创建图形界面



### 使用Apache及其他类型的日志文件



### SOAP和RESTful API通信

1. SOAP即简单对象访问协议（Simple Object Access Protocol），是一种允许程序进程使用不同操作系统的标准通信协议
2. SOAP同时也是一种Web技术，通过HTTP和XML通信
3. SOAP API主要用于创建，更新，删除和恢复数据等任务
4. 使用SOAP程序库
   1. 这里仅介绍Zeep库
   2. 用于WSDL文档，会生成服务和文档的代码，并为SOAP服务器提供编程接口
5. REST即表述性状态传递（Representational State Transfer），而RESTful API是一种用于Web服务开发的通信方式。
6. REST的优势在于占用宽带较少，适合网络应用。
7. RESTful API使用统一的接口，所有资源都由GET， POST， PUT和DELETE操作处理。
8. RESTful API独立处理每个请求，从客户端发送到服务器的请求必须包含让服务器理解该请求的所有信息，马科夫？

### 网络爬虫--从网站中提取有用的数据

### 统计信息的收集和报告

### MySQL和SQLite数据库管理

