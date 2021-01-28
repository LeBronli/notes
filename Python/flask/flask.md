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

#### 引导推荐的布局

#### 实现块组合和布局继承

