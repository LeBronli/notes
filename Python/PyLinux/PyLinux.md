### Python语言与Linux系统管理

1. 使用Python管理Linux的好处
   1. Python相对于Shell脚本，代码更加清晰
   2. Python语言表达能力强
   3. 可跨平台
   4. 可以方便地与操作系统集成：可以方便地在Python中执行Linux命令
   5. 许多开源的项目
   6. 有不少使用Python开发的自动化管理工具
   7. 扬长避短，因为Linux管理对执行速度没有要求，而是对于编写速度有要求

### Python生态工具

##### 内置小工具

1. 下载服务器

2. 字符串转换成JSON

   1. 在工作过程中，我们的系统会调用底层的API，而底层的API一般都是以JSON的格式返回，所以这猴子那个需求是很常见的

   2. 在JSON后使用`| python -m json.tool`

      `echo '{"job": developer, "name": "lmx"}' | python -m json.tool`

3. 检查第三方库是否正确安装

   1. 使用解释器的`-c`参数快速执行`import`语句：`python -m "import paramiko"`

##### pip高级用法

1. pip的优点

   1. 不只是提供安装，还提供卸载和显示已安装列表
   2. 能支持虚拟环境
   3. 可以通过`requirements.txt`集中管理依赖
   4. 可以处理二进制格式（`whl`）
   5. 是先下载后安装，就算安装失败也会清理干净

2. 只有打包上传到`pypi.python.org`的才能直接安装，对于其他的可以通过源码安装：

   ```bash
   git clone https://github.com/paramiko/paramiko.git
   cd paramiko
   python setup.py install
   ```

3. pip常见命令

   1. `install`
   2. `download`
   3. `uninstall`
   4. `freeze`：按照`requirements`格式输出安装包，从而可以让别的环境上直接安装一样的，其实就是输出本地环境安装的包
   5. `list`：列出当前系统中的安装包
   6. `show`：查看安装包的信息
   7. `check`：查看安装包的依赖是否完整
   8. `search`：查找安装包
   9. `wheel`：打包软件到whell格式

4. 使用豆瓣和阿里源加速pip安装

   1. 一次使用：`pip install -i https://pypi.douban.com/simple/ flask`

   2. 多次使用：

      1. 需要创建`~/.pip/pip.conf`文件，然后保存如下内容：

         ```python
         [global]
         index-url = https://pypi.douban.com/simple
         ```

##### Python编辑器

1. Linux中使用vim，其他系统中可以使用PyCharm
2. vim的一些功能
   1. 一键执行：是自定义的vim配置，在编写完Python代码后，直接`F5`执行代码，不用退出来
3. ipython的一些用处
   1. `?os.path.is*`，就可以返回这个模块中符合这种命名规范的函数
   2. `os.path.isfile?`，可以返回这个函数的说明
   3. `json.dump??`，会返回更加详细的信息，甚至包括源代码
   4. 获取对象的信息：
      1. `%pdef json.dump`
      2. `%pfile json.dump`
      3. `%pdoc json.dump`
      4. `%pinfo json`
   5. 以`%`开头的函数，被称为magic函数
      1. magic函数是专门为ipython提供的
      2. 可以通过`%`加`tab`来返回magic函数
      3. magic函数分为3类：
         1. 操作代码的
         2. 控制IPython的
         3. 其他
      4. 可以使用`%load`来将外部代码导入到IPython中执行：`%load connect.py`
   6. IPython还可以灵活的操作输入历史和输出历史
      1. `_i, _ii, _iii`保存最近的三次输入
      2. `_, __, ___`保存最近的三次输出
      3. `%edit`
      4. `%save`
      5. `%rerun`
   7. 与操作系统交互
4. jupyter

##### Python调试器

1. 标准库的`pdb`
   1. 启动方法：
      1. 在命令行使用参数：`python -m pdb test_pdb.py`
      2. 在脚本中使用`pdb.set_trace`方法设置断点，这样当运行到断点时，会暂停执行并打开pdb调试器
   2. 常用的调试命令
      1. 
2. 开源的`ipdb`
   1. 与`pdb`有相同的接口
   2. 但是具有语法高亮，tab补全，更友好的堆栈信息等高级功能，就像IPython之于python shell

##### 代码规范

1. 大家都遵守PEP8规范
2. 可以使用包`pycodestyle`来检查文件是否符合标准：`pycodestyle --show-source --show-pep8 test.py`
3. 使用包`autopep8`将代码格式化为PEP8风格：`autopep8 --in-place optparse.py`

##### Python工作环境管理

1. 使用`pyenv`来管理不同的Python版本
   1. 安装：
   2. 重新载入配置文件：
   3. 使用
      1. `pyenv install --list`查看可安装的Python版本
      2. `pyenv versions`查看系统中包含的Python版本
      3. `pyenv global x`改变默认的Python版本，依赖也会跟着变，不用担心
      4. `pyenv uninstall x`删除Python版本
2. 使用`virtualenv`管理不同的项目
   1. 用以隔离不同项目的工作环境。
   2. 注意`pyenv`和`virtualenv`的区别：
      1. `pyenv`用于管理不同的Python版本
      2. 而后者是用来隔离的
   3. 使用`pyenv-virtualenv`
      1. 安装：
      2. 重新载入配置文件
      3. 使用
         1. 新建工作环境：`pyenv virtualenv 2.7.13 first_project`
         2. 查看工作环境：`pyenv virtualenvs`
         3. 进入工作环境：`pyenv activate first_project`
         4. 退出工作环境：`pyenv deactivate`
         5. 删除虚拟环境：`pyenv virtualenv-delete first_project`

### 打造命令行工具

##### 与命令行相关的Python特性

1. `sys.argv`列表保存了所有命令行参数

   1. 第一个元素是命令行程序的名称
   2. 其余的命令行参数以字符串形式保存在该列表中
   3. 因为`argv`只是一个普通列表，所以可以直接修改其中的内容

2. shell脚本具有一个其他脚本语言都没有的有点，那就是管道

   1. 通过管道，我们可以用多个简单的命令来实现一个复杂的功能。
   2. `sys`库中，有3个文件描述符，不需要调用`open`函数就可以直接使用：
      1. `stdin`
         1. 是一个普通文件对象，除了从标准输入读取内容外，没有特殊之处
         2. 也可以使用`sys.stdin`调用文件对象的方法。
            1. `read`方法读取所有内容
            2. `readlines`方法将输入内容读取到一个列表中
      2. `stdout`
      3. `stderr`

3. `awk`是Linux下一个广泛使用的工具，但是完全可以用Python代替

   1. Python可以使用`fileinput`进行多文件处理
   2. 使用`fileinput.input()`按行读取内容即可
   3. `fileinput`比`stdin`更加灵活，因为它既可以从标准输入中读取数据，也可以从文件中读取数据

4. 使用`SystemExit`异常打印错误信息

   1. 如果我们程序失败，需要在标准错误中输出错误信息，然后以非零的返回码返回程序，就需要使用`std.err`

      ```python
      import sys
      
      sys.stderr.write('error message')
      sys.exit(1)
      ```

   2. 实现上面一样的效果，可以直接`raise SystemExit('error message')`

5. 使用`getpass`库获取密码

   1. `getpass.getuser()`
   2. `getpass.getpass('your password: ')`

##### 使用`ConfigParse`解析配置文件

1. 配置文件好处：不用每次使用的时候都指定相应的参数
2. `ini`格式配置文件具有与编程语言无关，可读性强和易于处理等优点，已经被广泛使用
3. `configparser`库中有一个`ConfigParser`类
   1. 这个类可以同时解析多个配置文件
   2. 配置的大致结构就是：章节下面有多个选项，每个选项对应着值
   3. 有许多方法，与读取配置文件，判断配置项相关的方法有：
      1. `.sections()`：返回一个包含所有章节的列表
      2. `.has_section(section_name)`：判断章节是否存在
      3. `.items()`：返回所有选项，以元祖形式
      4. `.options(option_name)`：返回一个包含章节下所有选项的列表
      5. `.has_option(option_name)`：判断某个选项是否存在
      6. `get, getboolean, getint, getfloat`获取选项的值
   4. 提供了修改配置文件的方法：
      1. `.remove_section`：删除章节
      2. `.add_section`：添加章节
      3. `.remote_option`：删除一个选项
      4. `set`添加一个选项：`cf.set('mysql', 'port', 3306)`分别传入章节，选项和值
      5. `write`：将对象中的数据保存到文件中
4. 使用方法：
   1. 先创建一个`ConfigParser`对象
   2. 多个参数，比较重要的是`allow_no_value`，默认为`False`
   3. 使用`read()`

##### 使用`argparse`解析命令行参数

1. 首先需要创建一个解析器：`parser = argparse.ArgumentParser()`
2. 初始化有多个参数，比较常用的`description`
3. 为应用程序添加参数选项：`add_argument()`
   1. `name of flag`：参数的名字
   2. `action`：遇到参数的动作，默认为`store`
   3. `nargs`：参数的个数
   4. `const`：需要的常量值
   5. `default`：不指定参数时的默认值
   6. `type`：参数的类型
   7. `choices`参数允许的值
   8. `required`：可选参数是否可以省略
   9. `help`：参数的帮助信息
   10. `metavar`：在usage说明中的参数名称
   11. `dest`：解析后的参数名称
4. 解析参数需要使用`parse_args`方法，返回一个`Namespace`对象，获取对象后，通过属性的方式进行访问
5. 会比较复杂，在后面会介绍一个更易用的开源库

##### 使用logging记录日志

1. 记录日志一般有2个用处

   1. 诊断日志：记录错误信息等
   2. 审计日志：为商业分析而记录的日志

2. 使用：

   1. 导入`logging`模块
   2. 创建对象：`logging.basicConfig(filename='app.log', level=logging.INFO)`
   3. 使用`debug, info, warn, error, critical`等函数记录日志

3. 默认情况下，将日志打印到终端，级别为WARNING，。

4. 日志分为5个等级

   1. CRITICAL
   2. ERROR
   3. WARNING
   4. INFO 
   5. DEBUG

5. 配置日志格式

   1. `logging`模块中的几个概念：

      1. Logger：日志记录器，是应用程序中能直接使用的接口
      2. Handler：日志处理器，用来表明将日志保存在什么地方，以及保存多久
      3. Formatter：格式化

   2. 一个记录器使用一个处理器，一个处理器使用一种格式，其实看下面日志配置文件的配置方法就能了解到了：

      ```ini
      //创建一个名为root的记录器
      [loggers]
      keys = root
      
      //创建一个名为logfile的处理器
      [handlers]
      keys = logfile
      
      //创建一个名为generic的格式
      [formatters]
      keys = generic
      
      //将root的处理器绑定为logfile
      [logger_root]
      handlers = logfile
      
      //设定handlers的配置
      [handler_logfile]
      class = handlers.TimedRotatingFileHandler
      args = ('app.log', 'midnight', 1, 10)
      level = DEBUG
      formatter = generic
      
      //设定格式的配置
      [formatter_generic]
      format = %(asctime)s %(levelname)-5.5s [%(name)s:%(lineno)s] %(message)s
      ```

##### 与命令行相关的开源项目

1. 使用`click`解析命令行参数

   1. 与标准库`argparse`相同，但是更加简单，好比`request`之于`urllib`

   2. 主要的改进在于易用性，使用click分为2个步骤：

      1. 使用`@click.command()`装饰一个函数，使其成为命令行接口
      2. 使用`@click.option()`等装饰函数，为期添加命令行选项等等

   3. 使用示例：

      ```python
      import click
      
      @click.command()
      @click.option('--count', default=1, help='Number of greetings')
      @click.option('--name', prompt = 'Your name', help = 'The person to greet')
      def hello(count, name):
          for x in range(count):
              click.echo('hello %s' % name)
              
      hello()
      ```

      使用时，可以在命令行中指定参数：`python hello.py --count=3 --name='John'`

      因为name参数使用了prompt，也就意味着我们可以先不在命令行中指定name，如果这个缺失，命令行会自动生成交互式来获得这个参数

   4. 如果想要让输入只是某些值中的一个，可以使用`choice`：`@click.option('--hash-type', type=click.Choice(['md5', 'sha1']))`

   5. 当我们要指定密码这种参数的时候，在命令行中直接指定不太安全，我们可以通过：

      1. 设置prompt来交互式地输入密码
      2. 然后再设置`hide_input`为True，这样就可以隐藏我们的命令行输入
      3. 设置`confirmation_prompt`为True，这样就可以确认密码

   6. fc命令，这个我不太懂------------------------------------------------------------------------------------

2. 使用`prompt_toolkit`库

   1. 用来处理交互式场景，因为有的时候程序需要与用户交互，但是用标准库太难用了，而这个库的好处是：

      1. 语法高亮
      2. 支持多行编辑
      3. 支持代码补全
      4. 支持自动提示
      5. 可以使用鼠标移动光标
      6. 支持Emacs和Vi风格的快捷键
      7. 支持查询历史
      8. 对Unicode友好
      9. 使用Python语言开发，跨平台

   2. 可以通过下面的设置来实现查看历史记录：

      ```python
      from prompt_toolkit import prompt
      from prompt_toolkit.history import FileHistory
      
      while True:
          user_input = prompt('>', history=FileHistory('history.txt'))
          print(user_input)
      ```

   3. 自动提示功能：

      1. 导入：`from prompt_toolkit.auto_suggest import AutoSuggestFromHistory`
      2. 加入参数：`auto_suggest=AutoSuggestFromHistory()`

   4. 使用tab键补全

      1. 导入：`from prompt_toolkit.contrib.completers import WordCompleter`
      2. 设定补全的集合：`SQLCompleter = WordCompleter(['select', 'from', 'insert', 'update', 'delete', 'drop'], ignore_case=True)`
      3. 加入参数：`completer=SQLCompleter`

### 文本处理

1. 虽然Linux上有很多处理文本的方法，但是也有很多限制：
   1. 无法再Windows下使用
   2. 对中文支持不友好
   3. 大量依赖正则表达式，学习曲线较为陡峭
2. 字符串常量
   1. 与C++等不同，Python不会区分字符和字符串，所以既可以用单引号也可以用双引号
   2. 可以使用原始字符串，就是在字符串前面加上一个`r`，他会抑制所有的转义，打印字符中的所有反斜杠
   3. Python中字符串的特点：
      1. 不可变
         1. 我们对字符串做的修改的函数都是返回一个新的字符串
         2. 所以要避免产生太多的中间结果，因为每个新字符串都会占用一块独立的内存
         3. 产生的中间结果越多，性能越差
      2. 是字符的有序集合
         1. 也就是可以切片和通过下标访问
   4. 字符串相关的函数：
      1. 大小写相关的：
         1. `upper`
         2. `lower`
         3. `isupper`
         4. `islower`
         5. `swapcase`
         6. `capitalize`
         7. `istitle`
      2. 判断类方法：
         1. `isalpha`
         2. `isalnum`
         3. `isspace`
         4. `isdecimal`
      3. 前缀后缀
         1. `startswith()`
         2. `endswith()`
      4. 查找类
         1. `find(sub_str, start_pos)`，如果查找失败，返回-1
         2. `index`，与find类似，但是如果查找失败会抛出`ValueError`异常
         3. `rfind`与find类似，但是是从后面开始查找
         4. `rindex`
      5. 操作方法
         1. `str.join(iterable)`
         2. `split`
         3. `strip`
         4. `rstrip`
         5. `lstrip`
3. 处理字符案例
4. 字符串格式化
   1. 存在2中格式化方法，即%表达式和format函数，但是format表达式才是未来，所以这里只介绍一种
   2. format的形式化定义：
      1. `format_spec`：`[[fill]aligh][sign][#][0][width][,][.precision][type]`
      2. `fill`：表示显式时需要填充的字符，默认为空格
      3. `aligh`：表示输出结果的对其方法。
         1. `<`左对齐
         2. `>`右对齐
         3. `^`居中
      4. `sign`：仅对数字起作用，表示显式数字是是否需要正负号
      5. `width`：表示显式的宽度
      6. `precision`：显示的精度
      7. `type`：显示的类型

##### 正则表达式

1. 正则表达式的基本语法：

   1. `^`：开始标记
   2. `$`：结束标记
   3. `.`任意字符
   4. `[]`匹配方括号内的任意一个字符
   5. `[^]`匹配方括号中之外的任意字符
   6. `[-]`陪陪方括号所写的范围内的任意字符：`[1-5]`
   7. `?`匹配之前项的1次或0次
   8. `+`匹配之前项的1次或多次
   9. `*`匹配之前项的0次或多次
   10. `{n}`匹配之前项的n次
   11. `{n,}`匹配之前项至少n次
   12. `{n, m}`匹配之前项的n-m次

2. 使用`re`库处理正则表达式

   1. `findall(re_pattern, str)`最常使用的函数，用来输出所有符合模式匹配的子串

      ```python
      re.findall(re_pattern, str, flags=re.IGNORECASE)
      ```

   2. 还有一种先编译正则表达式，然后再进行匹配：

      ```python
      re_obj = re.compile(re_pattern, flags=re.IGNORECASE)
      re_obj.findall(data)
      ```

   3. 两种方法性能上会有比较大的差距，所以处理大量文本的时候，用第二种更快

3. 常用的re方法

   1. `re.match(sub_str/re_pattern, str)`
      1. 功能上类似于`startswith`
      2. 匹配成功返回`SRE_Match`对象
         1. 这个对象可以使用`start()`和`end()`方法分别得知匹配子串的起始和结束位置
         2. `re`属性返回re_pattern
         3. `string`属性返回匹配的字符串
         4. `group()`方法返回匹配到的字符串
      3. 匹配失败返回None
   2. `search`
      1. 与match几乎一模一样，但是可以从任意位置开始匹配
      2. 但是只能匹配一处，如果想要匹配多处，可以使用`findall`和`finditer`
   3. `sub`
      1. 替换字符串
   4. `split`

##### 字符串编码

1. ASCII
   1. 用一个字节来存储字符
   2. 但是第一位都是0，所以只能表示128个字符
2. latin-1
   1. 就是在ASCII的基础上，把第一位用上了，所以可以表示255个字符
   2. 127以上的字符分配给了重音和其他特殊字符
   3. 所以其实和ASCII还是兼容的
3. Unicode
   1. 万国码，集合了所有字符
   2. 每个字符被映射到一个整型编码，也就是32位，编码范围是0-0x10FFFF
4. UTF-8
   1. 因为Unicode表示一个字符所占用的空间很多，而且它的容量远大于常用的字符集合，所以非常浪费
   2. 所以Unicode制定了各种存储编码的方式，被称为Unicode转换格式（UTF）。
   3. 每种UTF都会把一个Unicode编码转换为一个或多个编码单元，而UTF-8的编码单元就是8位的字节
   4. 通常我们使用UTF-8，因为：
      1. 采取字节为编码单元，不存在字节的大端和小端的问题
      2. 因为每个ASCII字符都只用一个字节取存储，也就是兼容ASCII
   5. 编码规则：
      1. 对于单字节符号，第一位设为0，也就是和ASCII是相同的
      2. 对于x字节的符号，第一个字节的前x位都设为1，第x+1位设为0，后面字节的前2位一律设为10，其他没有提及的二进制位，全部为这个符号的unicode码

##### Jinjia2模板

1. 常用于Web应用，这里用来设置配置文件

2. jinjia2可以用于任何基于文本的格式

3. 存在3种语法：

   1. 控制结构`{%%}`
   2. 变量取值`{}`
   3. 注释`{# #}`

4. Jinjia2用大括号区分代码和文本，可以使用for和if，但是后面要加上endfor和endif

5. 变量可以通过过滤器修改，过滤器可以理解为Jinja2里面的内置函数和字符串处理函数

   1. 过滤器与变量用管道（|）分割，多个过滤器可以链式使用
   2. 常见过滤器
      1. `safe`：渲染值时不转义
      2. `capitalize`
      3. `lower`
      4. `upper`
      5. `title`
      6. `trim`：去掉首尾空格
      7. `striptags`：渲染之前将值中所有的HTML标签都删掉
      8. `join`
      9. `replace`
      10. `round`
      11. `int`

6. 还定义了一些特殊的变量，我们不用定义就可以直接使用

   1. `loop.index`
   2. `loop.index0`
   3. `loop.revindex`
   4. `loop.revindex0`
   5. `loop.first`
   6. `loop.last`
   7. `loop.length`
   8. `loop.cycle`

7. jinja2的宏

   1. 这里的宏更可以理解为函数

   2. 使用关键字`macro`

   3. 定义部分使用函数名的标签包含

   4. 定义完毕要使用`endmacro`关键字

   5. 实例

      ```jinja2
      {% macro input(name, type='text', value='') %}
       <input type="{{type}}" name="{{name}}" value="{{value}}">
      {% endmacro %}
      ```

8. 如果使用jinja2进行web开发，继承是它最吸引人的功能

   1. `{% extends "base.html" %}

9. 实战

   1. 在flak中使用，只需要使用`render_template`函数即可

   2. 如果想要用其配置配置文件，则需要了解下面的：

      ```python
      from jinja2 import Environment, PackageLoader
      env = Environment(loader=PackageLoader('yourapplication', 'templates'))
      template = env.get_template('mytemplate.html')
      template.render(the='variables', go='here')
      ```

   3. 2中使用包加载器有个限制，那就是模板都应位于一个Python包下，为了避免这个，我们可以使用文件系统加载器：

      ```python
      import os
      import jinja2
      
      def render(tpl_path, **kwargs):
          path, filename = os.path.split(tpl_path)
          return jinja2.Environment(loader=jinja2.FileSystemLoader(path)).get_template(filename).render(**kwargs)
      ```

10. 使用Jinja2生成XML配置文件

    1. 这里演示将一个默认配置文件中的配置渲染到其他两个文件中
    2. 首先定义渲染函数，与上面的3中的`render`函数相同
    3. 然后定义读取配置文件并使其值能够被渲染函数访问：
    4. globals是什么东西？----------------------------------------

### Linux系统管理

1. 工程师管理Linux系统的时候，大多数情况下都是在管理文件，因为Linux中万物皆文件，这样处理的一个显著的好处就是能够在这些资源上使用相同的一组API

##### 文件读写

1. 使用`open`函数，接受文件名和打开模式作为参数，返回一个文件对象

   1. `r`：默认读模式，如果文件不存在，抛出`FileNotFoundError`异常
   2. `w`
   3. `x`：创建一个新的文件，如果文件已经存在，抛出`FileExistError`异常
   4. `a`：在文件末尾追加文件

2. 每打开一个文件就需要占用一个文件句柄，而一个进程拥有的句柄数是有限的。

3. 使用上下文管理器来防止句柄泄露

4. 文件读写操作：

   1. `read`：读取文件所有内容
   2. `readline`：一次性读一行
   3. `readlines`：将文件内容存在一个列表中，列表的每一行对应文件中的一行

5. 文件也是可迭代对象，可以对他使用`for`：

   ```python
   with open('data.txt') as inf:
       for line in inf:
           print(line.upper())
   ```

##### 文件和文件路径管理

1. 使用`os.path`进行路径和文件管理
   1. 可以使用`os.path.join`来连接路径和文件，从而来解决不同操作系统下路径分隔符不同的问题
   2. 还可以使用`os.sep`来表示不同操作系统下的路径分隔符
   3. 拆分路径：
      1. `split`：返回一个二元数组，包含文件的路径和文件名
      2. `dirname`：返回文件的路径
      3. `basename`：返回文件的文件名
      4. `splitext`：返回一个出去文件扩展名的部分和扩展名的二元数组
   4. 构建路径：
      1. `expanduser`：展开用户的HOME目录
      2. `abspath`：得到文件或者路径的绝对路径
      3. `join`：前面说过
   5. 获取文件属性
      1. `getatime`：获取访问时间
      2. `getmtime`：获取修改时间
      3. `getctime`：获取创建时间
      4. `getsize`获取文件大小
   6. 判断文件类型
      1. `exists`
      2. `isfile`
      3. `isdir`
      4. `islink`
      5. `ismount`
2. 用os模块管理文件和目录
   1. 操作文件和目录
      1. `unlink/remove`：删除指向的文件
      2. `rmdir`删除目录
      3. `mkdir`创建目录
      4. `rename`重命名文件或者目录
      5. `getcwd`获取目前所在的目录
      6. `chdir`切换工作目录
   2. 修改和判断文件权限
      1. `chmod`修改文件的权限
      2. `access`判断文件是否有相应的权限
         1. 权限有下面3中，os模块也提供了三个常量来表示：
            1. `R_OK`
            2. `W_OK`
            3. `X_OK`可执行

##### 查找文件

1. 使用`fnmatch`
   1. `fnmatch`：判断文件是否符合特定的模式
   2. `fnmatchcase`：不区分大小写
   3. `filter`：返回符合的文件列表
   4. `translate`：将通配符模式转换为正则表达式
2. 使用`glob`
   1. 如果使用`fnmatch`，一般是先用`os.listdir`获取文件列表，然后用其中的方法来过滤
   2. 但是使用`glob`可以更方便，可以直接过滤：`glob.glob('*.txt')`
3. 使用`os.walk`遍历目录树
   1. 它会遍历某个目录及其子目录，对于每个目录，返回一个三元组，元素分别是：
      1. `dirpath`：当前目录
      2. `dirnames`：当前目录下的子目录
      3. `filenames`：当前目录下的文件
4. 案例

##### 高级文件处理接口`shutil`

1. `shutil`模块和`os`模块有一些功能是重合的，原因就在于标准库的定位问题：
   1. `os`是对操作系统的接口进行封装，主要作用是跨平台
   2. `shutil`主要作用是管理文件和目录
2. 对于常见的文件操作，`shutil`会更加易于使用，我们应该尽量使用这里面的函数
3. `shutil.copy`：拷贝文件
4. `shutil.copytree`：拷贝目录
5. `shutil.move`：与Linux下的`mv`几乎一样
6. `shutil.rmtree`与os中的不同，它并不要求被删除的目录或文件非空，就算是空的，也不会报错

##### 文件内容管理

##### 使用Python管理压缩包

##### Python中执行外部命令

1. `subprocess`模块用于创建和管理子进程，提供了高层次的接口
2. 提供一个名为`Popen`的类来启动和设置子进程的参数，由于这个类比较复杂，还提供了一些封装函数来更方便地使用
3. 首先使用封装的类，如果不能满足需求，才会去使用`Popen`类
4. `subprocess.call(args, *, stdin=None, stdout=None, stderr=None, shell=False)`
   1. 返回命令的退出状态码
   2. 将命令以列表的形式传入：`subprocess.call(['ls', '-l'])`来执行`ls -l`的shell命令
5. `subprocess.check_call()`
   1. 与`call`类似，区别在于异常情况下返回的形式不同，执行成功返回0，不成功抛出`subprocess.CalledProcessError`异常
6. `subprocess.check_output`
   1. 上面两个函数都是直接将输出放到终端中，这种返回的形式可能不是你想要的
   2. 其实就是将返回的内容放到返回的对象中，以便于进一步处理
   3. 同样靠抛出异常来表示执行错误
7. `subprocess.Popen(args, bufsize=0, executable=None, stdin=None, stdout=None, stderr=None, preexec_fn=None, close_fds=False, shell=False, cwd=None, env=None, universal_newlines=False, startupinfo=None, creationflags=0)`
   1. 这个对象创建后，子进程就会运行，类提供了一些方法来控制子进程的执行
      1. `wait`：等待子进程结束
      2. `poll`：检查子进程状态
      3. `kill`：给子进程发送SIGKILL信号终止子进程
      4. `send_signal`：向子进程发送信号
      5. `terminate`：给子进程发送SIGTERM信号终止了子进程
      6. `communicate`：与子进程交互

##### 使用Python部署MongoDB

### 使用Python监控Linux系统

### 文档和报告

### 网络

##### 列出网络上所有活跃的主机

1. 可以直接使用主机名，域名或者IP地址作为参数调用ping命令：`ping www.baidu.com`
2. ping命令会连续发送数据包，并将结果打印到终端上，如果主机不可达，会显示`Destination Host Unreachable`的错误信息
3. ping命令还可以用于估计主机之间的延迟情况
4. 默认情况下，ping命令会不停地发送数据报，直到终止命令。可以使用`-c`选项限制发送的`ECHO_REQUEST`数据报数量
5. 因为使用ping命令要等很久，所以如果要检测大量主机，可以考虑使用并发

##### 端口扫描

1. Linux下，可以使用`telnet`命令判断一个端口是否打开
2. 但是这个命令有个问题，那就是如果访问一个不可达的端口的时候，要很久才能超时返回，而且该命令没有参数可以控制超时时间
3. 使用`socket`模块
   1. `socket.socket`
      1. 工厂函数，返回一个`socket`对象
      2. 可以通过传递参数来创建不同网络协议和网络类型的对象
      3. 默认情况下，会创建使用TCP协议的对象
   2. `s.connect()`连接服务器的端口
   3. `s.close()`关闭
   4. `s.send()`发送请求
   5. 这里使用`socket`对象来扫描端口其实就是通过`connect`方法来连接不同的端口，通过返回值来判断是否可用
4. 还可以使用`telnetlib`模块
   1. 包含一个`Telnet`类，一个这个对象表示一个telnet的连接
   2. 创建一个这个对象并不会建立到远程主机的连接，需要使用`open`函数，并传入：
      1. 主机名
      2. 端口号
      3. 超时时间
5. 使用`nmap`扫描端口
   1. `nmap`是`Network Mapper`的简称，可以进行
      1. 主机发现
      2. 端口扫描
      3. 版本侦测
      4. 操作系统侦测
   2. 它是网络管理员必备的软件之一
   3. `-sL`选项指定IP地址
   4. 还可以通过把IP地址保存到文件中，然后通过`-iL`选项读取文件中的IP地址
   5. 使用`-sP`或者`-sn`选项可以告诉nmap不要进行端口扫描，只要判断主机是否可达就行
   6. 使用`nmap`+IP地址就可以直接进行端口扫描，默认下会扫描1000个最常用的端口号
   7. 扫描端口时，提供了大量参数来控制：
      1. 端口扫描协议：`T(TCP), U(UDP), S(SCTP), P(IP)`
      2. 扫描类型
      3. 扫描的端口号：`-p 80, 443 -p 80-160`
   8. 将端口分成6个状态：

##### 网络嗅觉器Scrapy

1. 可以发送，嗅探，剖析和伪造网络数据包
2. 使用Scrapy发送数据报
   1. 遵循网络协议中经典的TCP/IP四层模型：
      1. 链路层
      2. 网络层
      3. 运输层
      4. 应用层
   2. Scrapy为这些层都提供了辅助类，我们要做的就是将它们实例化并修改对象的取值，不同层之间用`/`来连接
   3. `.display()`方法可以查看当前数据报的内容，即各个参数的取值情况
   4. 

### Python自动化管理

### 深入浅出Ansible

### 使用Python打造MySQL专家系统