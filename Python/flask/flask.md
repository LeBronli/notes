### 配置Flask

#### 使用virtualenv配置开发环境

1. 使用`virtualenv name`来创造虚拟环境
2. 想要使用虚拟环境，有几步：
   1. `cd file_name`进入虚拟环境的文件夹
   2. `source bin/activate`激活虚拟环境
3. 使用`deactivate`来退出虚拟环境

#### 处理基本的配置问题

1. Flask中，某些配置是通过Flask对象中名为config的属性实现的：
   1. 使用debug模式运行应用程序：`app.config['DEBUG'] = True`
   2. debug模式还可以在环境变量上设置
2. 启用debug模式将在代码发生变化时使得服务器自身重载，并在出现错误时提供非常有用的Werkzeug调试器
3. 获得配置信息的方法：
   1. 



#### 配置基于类的设置

#### 组织静态文件

1. Flask推荐了一种组织方法：

   ```python
   my_app/
   	-app.py
       -config.py
       -__init__.py
       -static/
       -css/
        -js/
        -images/
       	-logo.png
   ```

2. 如果名为static的文件夹位于应用程序的根级别，也就是与app.py级别相同，那么Flask会自动读取这个文件夹中的内容，且无需额外的配置信息

3. 当在app.py文件中定义应用程序时，还可以向应用程序对象中提供名为static_folder的参数



#### 特定于部署的实例文件夹

1. 

#### 视图和模型的组合

1. 

#### 利用Blueprint创建模块化的Web应用程序

#### 通过setuptools安装Flask应用程序

1. 利用setuptools这个库，Flask应用程序的安装过程将变得十分简单。
2. 我们将依赖库写在一个setup.py文件中，然后使用命令：`python setup.py install`





### 基于Jinja2的模板机制

1. Flask可以在不需要第三方模板引擎的情况下编写完整的应用程序，比如上面的helloworld。但是在设计数千行HTML之类的代码的大型程序中，这种编写方法就不可行了
2. Flask默认支持Jinja2模板

3. 引导推荐的布局

   1. 默认情况下，Flask期望模板置于应用程序级别的，名为template的文件夹中

   2. 如果这个template文件夹存在，Flask将以此并通过`render_template()`方法自动读取内容

   3. 一般`render_template`函数会直接找template文件夹中的文件进行渲染：

      1. `render_template('index.html', user=user)`
      2. 这句话会让Flask找template文件夹中寻找index.html这个文件，然后在其中寻找`{{user}}`这个占位符，然后用上面定义的user来替代掉

   4. 一般的文件布局

      ```python
      flask_app/
      	-run.py
          my_app/
          	-__init__.py
              -product/
              	-__init__.py
                  -views.py
                  -model.py
              -templates/
              	-base.html
                  -home.html
                  -product.html
              -static/
              	-js/
                  	-bootstrap.min.js
                  -css/
                  	-bootstrap.min.css
                      -main.css
      ```

      1. template文件夹中的是html模板

   5. template中的模板一般是继承关系：

      1. 第一个模板被定义为所有模板的基模板，命名为`base.html`
      2. 

4. 创建自定义上下文处理器
   1. Jinjia2认为：逻辑处理行为应当在视图中完成，而非模板，以此保持模板的整洁
   2. 在这种情况下，上下文处理器成为了一种方便的工具，因为可以前面将数值传递至某个方法中，然后后面可以处理返回结果值
   3. 在视图文件中定义上下文处理器
   4. 上下文可以看成一个字典，可以添加或者移除值，通过`@product_blueprint.context_processor`修饰的任意方法均返回一个字典
5. 创建自定义的Jinjia2过滤器
   1. 



### Flask中的数据模型

### 与视图协同工作

### WTForms

### Flask中的身份验证机制

### 构建RESTful API

### Flask应用程序管理接口

### 国际化和本地化

### 调试，错误处理和测试

### 部署和后期部署

### 微服务和容器

### 提示和技巧





















### 快速上手

##### 一个最小的应用

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World'
```

1. 首先需要导入`Flask`类，这个类的实例会成为我们的WSGI应用
2. 然后创建一个实例，也就是`app`对象。要传入参数，这个参数的意义是告诉Flask在哪里可以找到模板和静态资源之类的东西
3. 修饰器`@app.route('/')`用来告诉Flask触发函数的URL
4. 函数要最后返回浏览器中显示的信息

1. 不要使用`flask.py`这个名字，会有冲突

2. 运行Flask应用

   1. ```bash
      export FLASK_APP=hello.py
      flask run
      ```

   2. ```bash
      export FLASK_APP=hello.py
      python -m flask run
      ```

3. 如果要打开所有的开发功能（包括调试模式），那么要在运行服务器之前设置FLASK_ENV环境变量

   ```bash
   export FLASK_ENV=development
   
   ```

   

   ##### 路由：

   1. 使用`route()`装饰器来将函数绑定到URL上

   2. 绑定变量在URL上：

      1. 使用`<variable_name>`，下面的函数就可以使用

         ```python
         @app.route('/user/<username>')
         def show_user_profile(username):
             return 'User %s' % escape(username)
         ```

      2. 使用`<converter:variable_name>`

         ```python
         @app.route('/post/<int:post_id>')
         def show_post(post_id):
             return 'Post %d' % post_id
         ```

      3. 转换器类型：

         1. `string`
         2. `int`
         3. `float`
         4. `path`
         5. `uuid`

   3. URL的尾部有没有斜杠都会转到有斜杠的那个页面上去，所以不要为有斜杠和没有斜杠都分别定义函数，这样会报错

   4. `url_for()`函数用于构建指定函数的URL。

      1. 参数

         1. 函数名作为第一个参数
         2. 可以接受任意个关键字参数，每个关键字参数对应URL中的变量
         3. 未知变量将添加到URL中作为查询参数

      2. 存在的意义

         1. 这个函数通常描述性更好
         2. 可以将改变URL的地方都集中在一起，不用到处取找
         3. 生产的路径总是绝对路径，可以避免相对路径产生副作用
         4. 如果你的应用是放在URL根路径之外的地方，`url_for()`会为你妥善处理

      3. 示例

         ```python
         from flask import Flask, escape, url_for
         
         app = Flask(__name__)
         
         @app.route('/')
         def index():
             return 'index'
         
         @app.route('/user/<username>')
         def profile(username):
             return '{}\'s profile'.format(escape(username))
         
         with app.test_request_context():
             print(url_for('index'))
             print(url_for('login', next='/'))
             print(url_for('profile', username='John Doe'))
         ```

      ##### HTTP方法

      1. 缺省情况下，一个路由只回应`GET`请求，可以使用`route()`装饰器的`method`参数来处理不同的HTTP方法

      2. 示例

         ```python
         from flask import request
         
         @app.route('/login', methods=['GET', 'POST'])
         def login():
             if request.method == 'POST':
                 return do_the_login()
             else:
                 return show_the_login_form()
         ```

      ##### 静态文件

      1. 也就是CSS和JavaScript文件，位于`/static`文件夹中

      ##### 渲染模板

      1. 在Python内部生成HTML不好玩，因为你要自己为HTML转义以确保安全，因为，Flask自动为你配置Jinjia2模板引擎
      2. 可以使用`render_template()`方法渲染模板，只需要提供模板名称和变量就行
      3. 模板内部可以和访问`get_flashed_messages()`函数一样访问`request, session和g`对象
      4. 模板可以继承，很有用
      5. 自动转义默认开启

      ##### 操作请求数据

      1. 通过本地环境来保持线程安全

         1. 某些对象在Flask中是全局对象，但是不是通常意义下的，而是特定环境下本地对象的代理
         2. 

      2. 在Flask中由全局对象`request`来提供请求信息

         1. 所以请求对象要先导入对象request

         2. 示例

            ```python
            @app.route('/login', methods=['POST', 'GET'])
            def login():
                error = None
                if request.method == 'POST':
                    if valid_login(request.form['username'], request.form['password']):
                        return log_the_user_in(request.form['username'])
                    else:
                        error = 'Invalid username/password'
                return render_template('login.html', error=error)
            ```

      ##### 文件上传

      1. 只需要在HTML表单中设置`enctype="multipart/form-data"`属性

      2. 已上传的文件被存储在内存或者文件系统的临时位置

         1. 可以通过`request`的`files`属性来访问上传的文件

         2. 可以通过`save()`方法将上传的文件存储到服务器的文件系统

         3. 可以通过`filename`属性来知道文件上传之前其在客户端中的名称

         4. 如果想要把客户端的文件名作为服务器上的文件名，可以通过`Werkzeug`提供的`secure_filename()`函数

         5. 示例：

            ```python
            from flask import request
            from werkzeug.utils import secure_filename
            
            @app.route('/upload', methods=['GET', 'POST'])
            def upload_file():
                if request.method == 'POST':
                    f = request.files['the_file']
                    f.save('/var/www/uploads/' + secure_filename(f.filename))
            ```

      ##### Cookies

      1. 访问cookies，可以使用request对象的`cookies`属性，得到一个包含了客户端传输的所有cookies的字典

      2. 可以使用`set_cookie()`方法来设置cookies

      3. 如果使用会话，就不要直接使用cookies，因为会话更安全点

      4. 示例：

         ```python
         from flask import request, make_response
         
         @app.route('/')
         def index():
             //获取cookies
             username = request.cookies.get('username')
             
             //设置cookies
             resp = make_response(render_template(...))
             resp.set_cookie('username', 'the username')
             return resp
         ```

      5. 使用延迟的请求回调方案可以在没有响应对象的情况下设置一个cookies

      

      ##### 重定向和错误

   1. 使用`redirect()`可以重定向

   2. 使用`abort()`可以提出退出请求，并返回错误代码

   3. 缺省情况下每种出错代码都会对应显示一个黑白的出错页面，使用`errorhandler()`装饰器可以定制出错页面

      ```python
      from flask import render_template
      
      @app.errorhandler(404)
      def page_not_found(error):
          return render_template('page_not_found.html'), 404
      ```

   ##### 关于响应

   1. 视图函数的返回值会自动转换成一个响应对象。

      1. 如果返回值是一个字符串，会被转换成：
         1. 一个包含作为响应体的字符串
         2. 一个`200 OK`出错代码
         3. 一个`text/html`类型的响应对象
      2. 如果返回值是一个字典：会调用`jsonify()`来产生一个响应
      3. 如果返回值是一个元组，那么元组中的项目可以提供额外的信息。
      4. 如果以上都不是，那么Flask会假定返回值是一个有效的WSGI应用，并把它转换为一个响应对象

   2. 如果想要在视图内部掌握响应对象的结果，可以使用`make_response()`函数

      1. 原理就是生成一个响应对象并返回，这样Flask就不会自动转换了

      ```python
      @app.errorhandler(404)
      def not_found(error):
          resp = make_response(render_template('error.html'), 404)
          resp.headers['X-Something'] = 'A value'
          return resp
      ```

   

   ##### 会话

   1. `session`的对象，允许在不同请求之间储存信息

   2. 这个对象相当于密钥签名加密的cookie，就是可以查看，但是如果没有密钥就没办法修改

   3. 使用之前设置一个密钥

      ```python
      from flask import Flask, session, redirect, url_for, escape, request
      
      app = Flask(__name__)
      
      app.secret_key = b'fasdfadfadafs'
      
      @app.route('/')
      def index():
          if 'username' in session:
              return 'Logged in as %s' % escape(session['username'])
          return 'You are not logged in'
      
      @app.route('/login', methods=['GET', 'POST'])
      def login():
          if request.method == 'POST':
              session['username'] = request.form['usernanme']
              return redirect(url_for('index'))
          return '''
          	<form method="post">
          		<p><input type=text name=username>
          		<p><input type=submit value=Login>
          	</form>
          	'''
      
      @app.route('/logout')
      def logout():
          session.pop('username', None)
          return redirect(url_for('index'))
      ```

   4. 

   ##### JSON格式的API

   1. 可以使用`jsonify()`函数，可以序列化任何支持的JSON数据类型

   ##### 其他

   1. 消息闪现：
      1. 提供一个易用的反馈方式，基本原理是在请求结束时记录一个消息，提供且只提供给下一个请求使用
      2. 通常通过一个布局模板来展现闪现的消息
      3. `flash()`用于闪现一个消息
      4. 在模板中，使用`get_flashed_messages()`来操作消息
   2. 日志
      1. 使用`app.logger`
   3. 集成WSGI中间件
      1. 可以通过包装内部的WSGI应用

   

   

   ### 教程

   ##### 项目布局

   

   ##### 应用设置

   1. 一个Flask应用是一个Flask类的实例，应用的所有东西都会和这个实例一起注册

   2. 创建Flask应用最简单的方法就是在代码最开始直接创建一个全局Flask实例。但是这种方法当项目越来越大的时候都会有些力不从心

   3. 所以我们可以在一个函数内部创建Flask实例来代替创建全局实例，并返回这个实例，这个函数称为**应用工厂**

   4. 应用工厂

      1. 创建`flaskr`文件夹，并添加`__init__.py`文件。

      2. `__init__.py`文件有2个作用

         1. 包含应用工厂
         2. 告诉Python：`flaskr`文件夹应当视为一个包

      3. 示例

         ```python
         import os 
         from flask import Flask
         
         def create_app(test_config=None):
             //创建实例
             app = Flask(__name__, instance_relative_config=True)
             
             //配置config
             app.config.from_mapping(
             	SECRET_KEY='dev',
             	DATABASE=os.path.join(app.instance_path, 'flaskr.sqlite'),
             )
             
             if test_config is None:
                 app.config.from_pyfile('config.py', silent=True)
             else:
                 app.config.from_mapping(test_config)
              
             //保证这个目录存在
             try:
                 os.makeddirs(app.instance_path)
             except OSError:
                 pass
             
             @app.route('/hello')
             def hello():
                 return 'Hello, Wrold'
             
             return app
         ```

      4. `create_app`是一个应用工厂函数

         1. 创建实例
         2. 设置缺省配置

   5. 运行应用

   

   ##### 定义和操作数据库

   1. 可以使用`SQLite`数据库来存储用户和博客内容，好处是：

      1. 不需要单独配置一个数据库服务器
      2. 提供了内置支持

   2. SQLite写入会比较慢，小应用不要紧，但是大应用就要考虑换了

   3. Python内置了`sqlite3`模块来支持SQLite数据库

   4. 连接数据库

      ```python
      import sqlite3
      
      import click
      from flask import current_app, g
      from flaks.cli import with_appcontext
      
      def get_db():
          if 'db' not in g:
              g.db = sqlite3.connect(
              	current_app.config['DATABASE'],
              	detect_types=sqlite3.PARSE_DECLTYPES
              )
              g.db.row_factory = sqlite3.Row
              
          return g.db
      
      def close_db(e=None):
          db = g.pop('db', None)
          
          if db is not None:
              db.close()
      ```

      1. `g`是一个特殊对象，独立于每一个请求。它可以用于储存可能多个函数都会用到的数据
      2. `current_app`也是一个特殊对象，指向处理请求的Flask应用

   5. 创建表

      1. 首先创建`db.sql`文件来储存创建空表的SQL命令

      2. 然后在`db.py`中添加Python函数，用于执行这个SQL命令：

         ```python
         def init_db():
             db = get_db()
             
             with current_app.open_resource('schema.sql') as f:
                 db.executescript(f.read().decode('utf8'))
                 
         @click.command('init-db')
         @with_appcontext
         def init_db_command():
             init_db()
             click.echo('Initialized the database')
         ```

         1. `open_resource()`打开一个文件
         2. `click.command()`定义一个名为`init-db`命令行，它调用`init_db`函数，并为用户显示一个成功的消息
         3. <font color='red'>不晓得前面那么多修饰器是干什么用的</font>

   6. 在应用中注册

      1. 上面的函数需要在应用实例中注册，不然的话没办法使用。
      2. 先在`db.py`文件中定义一个注册函数

      ```python
      def init__app(app):
          app.teardown_appcontext(close_db)
          app.cli.add_command(init_db_command)
      ```

      		1. `app.teardown_appcontext()`告诉Flask在返回响应进行清理的时候调用这个函数，有点像pytorch里面的那些
        		2. `app.cli.add_command()`添加一个新的可以与flask一起工作的命令

      1. 然后在工厂函数中使用这个注册函数

      ```python
      def create_app():
          ...
          
          from . import db
          db.init_app(app)
          
          return app
      ```

   7. 初始化数据库文件

      1. 使用命令行运行`init-db`这个命令：`flask init-db`
      2. 之所以可以这样用，是因为前面已经用`@click.command('init-db')`注册了<font color='red'>这里还是有点不懂，还有就是顺序，是先初始化数据库，然后再运行应用？</font>
      3. 使用这个命令之后，会有一个`flaskr.sqlite`文件夹出现在项目所在文件夹的`instance`文件夹中

   

   ##### 蓝图和视图

   1. 视图是一个应用对请求进行响应的函数

   2. Flask通过模型把进来的请求URL匹配到相应的处理视图->视图返回数据->把数据变成出去的响应

   3. 蓝图是一种组织一组相关视图以及其他代码的方式。

   4. 将视图注册到蓝图，然后再在工厂函数中将蓝图注册到应用。而不是直接将视图注册到引用，这样也缺失更好管理

   5. `Flaskr`有2个蓝图：

      1. 一个用于认证功能
      2. 另一个用于博客帖子管理

   6. 写认证蓝图

      ```python
      flaskr/auth.py
      import functools
      
      from flask import (
      	Blueprint, flask, g, redirect, render_template, request, session, url_for
      )
      from wekzeug.security import check_password_hash, generate_password_hash
      from flaskr.db import get_db
      
      bp = Blueprint('auth', __name__, url_prefix='/auth')
      
      flaskr/__init__.py
      def create_app():
          ...
          
          from . import auth
          app.register_blueprint(auth.bp)
          
          return app
      ```

      1. 这里其实也说明了一点，蓝图其实就是一个处理以某个url开头的视图的集合
      2. 通过`register_blueprint`方法来注册蓝图实例

   7. 认证蓝图的第一个视图：注册

      1. 处理URL为`/auth/register`的视图

      ```python
      flaskr/auth.py
      @bp.route('/register', methods=('GET', 'POST'))
      def register():
          if request.method == 'POST':
              username = request.form['username']
              password = request.form['password']
              db = get_db()
              error = None
              
              if not username:
                  error = 'Username is required'
              elif not password:
                  error = 'Password is required'
              elif db.execute(
              	'SELECT id FROM user WHERE username = ?', (username,)
              ).fetchone() is not None:
                  error = 'User {} is already registered.'.format(username)
                  
              if error is None:
                  db.execute(
                  	'INSERT INTO user (username, password) VALUE (?, ?)',
                  	(username, generate_password_hash(password))
                  )
                  db.commit()
                  return redirect(url_for('auth.login'))
              
              flash(error)
              
          return render_template('auth/register.html')
      ```

      1. `@bp.route`修饰器将视图注册到蓝图上
      2. `url_for`根据登录视图的名称生成响应的URL，使用它而不是直接写url的好处是，如果以后需要改的话，就不用修改所有涉及URL的代码

   8. 认证蓝图的第二个视图：登录

      1. 其他的与注册没什么不一样，不同点主要在于

         1. 使用`check_password_hash()`安全地比较哈希值

         2. 使用session

            ```python
            if error is None:
                session.clear()
                session['user_id'] = user['id']
                return redirect(url_for('index'))
            
            flash(error)
            ```

         3. 保持登录状态

            ```python
            @bp.before_app_request
            def load_logged_in_user():
                user_id = session.get('user_id')
                
                if user_id is None:
                    g.user = None
                else:
                    g.user = get_db().execute(
                    	'SELECT * FROM user WHERE id = ?', (user_id,)
                    ).fetchone()
            ```

            1. `bp.before_app_request`这个修饰器表示只要是这个蓝图内的，不管URL是什么，都会在请求之前先调用这个函数，来获取用户数据，保持登录状态

   9. 认证蓝图的第三个视图：注销

      1. 需要把用户id从`session`中移除
      2. 示例

      ```python
      @bp.route('/logout')
      def logout():
          session.clear()
          return redirect(url_for('index'))
      ```

   10. 在其他视图中验证

       1. 因为很多操作只能在登录之后才能做

       ```python
       flaskr/auth.py
       def login_required(view):
           @functools.wraps(view)
           def wrapped_view(**kwargs):
               if g.user is None:
                   return redirect(url_for('auth.login'))
               
               return view(**kwargs)
           return wrapped_view
       ```

       2. 装饰器返回一个新的视图，包含了原视图。但是新的会多检查一下用户是否载入，如果载入，就执行原视图，如果没有，就重定向到登录页面

   

   ##### 模板

   1. 

   

   

   



































