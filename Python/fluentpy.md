### 序言

#### Python数据结构

1. 类似于`__getitem__`这类前后都有双下划线的方法，叫做magic method，也就是魔法方法，也叫special methods。它们也简称为'dunde-getitem'，所以魔法方法也叫作dunder method
2. 使用魔法方法有2个好处
   1. 不用去记忆特定类的特定方法
   2. 更容易使用Python的标准库（因为标准库可以直接使用魔法方法，而不用知道具体的类方法）
3. Special Methods
   1. 首先这些方法是被编译器调用的，而不是你
   2. 对于自己定义的类，使用比如说`len()`时，编译器会自动调用`__len__()`方法
   3. 但是对于内置的类，编译器进行了优化，返回的是C struct中的一个叫做`ob_size`的值，这比对自定义的类使用要快很多
   4. 更多时候`special methods`是被隐式调用的，比如使用`for i in x`的时候，实际上会调用`iter(x)`，也就是调用`x.__iter__()`
   5. 除非在进行元编程，不然的话不要经常使用特殊方法
   6. 唯一经常使用的特殊方法是初始化函数，用来进行超类的初始化：`super().__init__()`
4. String representation
   1. 调用`repr`函数的时候，会自动调用`repr`这个特殊方法，得到一个对象的string representation
   2. console和debugger都会每次后调用`repr`这个函数
   3. 使用`repr`得到的可以使用`format`来映射到`%r`中
   4. 要与`__str__`区分开，后者是`print()`和`str()`会自动调用的。两者功能不太相同，前者是给开发者看的，后者是给终端用户看的
   5. 当为了图方便，只在这两种里面选一种实现，选`repr`，因为当没有`__str__`有用时，会自动调用`__repr__`
5. 运算
   1. 定义`__add__`和`__mul__`等特殊方法，就可以使用`+和*`等运算方法
6. 自定义类型的布尔值
   1. 定义该类型是True还是False
   2. 默认情况下，对象默认为True。
   3. 一般来说检查布尔值会调用特殊方法`__bool__()`，如果没有定义，会调用特殊方法`__len__()`。如果结果不是0，那就是True。
7. 特殊方法综述
   1. String/bytes representation：`__repr__, __str__, __format__, __bytes__`
   2. 转变成数字：`__abs__, __bool__, __complex__, __init__, __float__, __hash__, __index__`
   3. Emulating collections：`__len__, __getitem__, __setitem__, __delitem__, __contains__, __iter__, __reversed__, __next__`
   4. 遍历：`__iter__, __reversed__, __next__`
   5. Emulating callables：`__call__`
   6. 上下文管理：`__enter__, __exit__`
   7. 实例创造和销毁：`__new__, __init__, __del__`
   8. 属性管理：`__getattr__, __getattribute__, __setattr__, __delattr__, __dir__`
   9. 属性描述：`__get__, __set__, __delete__`
   10. 类的服务：`__prepare__, __instancecheck__, __subclasscheck__`
   11. 操作相关特殊方法



### 数据结构

#### 序列

##### 内置序列综述

1. Container sequences可以包含不同类型的对象：`list, tuple, collections.deque`
   1. 可以装不同类型的对象的原因是：包含的只是对象的reference
2. Flat sequences包含同一类型的对象：`str, bytes, bytearry, memoryview, array.array`
   1. 包含的是对象的值而不是ref，所以只能是同一类型

1. 内置序列中，除了`tuple, str, bytes`外所有的序列都是mutable的

2. 继承图示

   ![fluentpy1](/home/lzh/Pictures/fluentpy1.png)

3. list作为mutable和container sequence，使我们使用最多的类型

4. 在`(), {}, []`中，行的break是被忽略的，也就是说可以任意分行，不会造成语法错误

5. 类似于`[Card(rank, suit) for suit in self.suits for rank in self.ranks]`这种，会生成list，称为listcomp。我们可以通过这种表达式生成其他的sequence。比如：`tuple([ord(symbol) for symbol in symbols])`这样会生成一个tuple

   ##### genexp

   1. 上面`tuple([ord(symbol) for symbol in symbols])`这一种会很消耗memory，因为本质上是先生成list，然后将整个list传入tuple的构造函数来生成tuple。
   2. 所以，有一种更优的方法，那就是直接使用genexp，也就是：`tuple(ord(symbol) for symbol in symbols)`。
   3. 虽然两者看起来差别不大，但是genexp实际上是先生成一个生成器，然后再用tuple(generator)来使用完生成器从而得到一个tuple。所以元素是一个一个地转变的，也就是将symbol一个一个地传入tuple构造函数来转变，非常节省内存
   4. 使用`()`的是genexp，得到的是生成器
   5. 使用`[]`的是listcomp，得到的是list

   ##### tuple

   1. 不能只是把tuple看做是一个不能改变的list

   2. tuple实际上有2种使用场景

      1. immutable list，保证里面的值不被改变
      2. records with no field names，这是下面主要讨论的

   3. 可以使用*这个符号来unpack tuple

      ```python
      t = (20, 8)
      divmod(*t)
      //等价于下面的
      divmod(20, 8)
      ```

   4. 当我们只想要tuple的一部分时

      1. 可以用_这个dummy varialbe，也就是把它当成一个占位符：`_, filename = (20, 8)`
      2. 只想要其中的一部分时，可以使用*：`a, b, *rest = range(100)`这样做后，rest会变成一个list，存储剩下来的元素

      ##### named tuples

      1. 可以使用`collections.namedtuple`这个factory来生产named tuples

      2. 其实作用类似于C中的struct，可以作为很好的存储数据的结构

      3. 和普通tuples用一样的空间，因为field names存储在class中

      4. 所使用的空间比通常的类对象少，因为不将数据存储在per-instance `__dict__`

      5. 使用方法

         ```python
         from collections import namedtuple
         //定义好
         City = namedtuple('City', 'name country population coordinates')
         //定义好实例，传入数据
         tokyo = City('Tokyo', 'JP', 36.933, (35.453, 5432.54235))
         ```

         1. 需要提前定义好名字和fields：第一个参数是名字，第二个参数是fields
         2. 然后再传入数据

      6. `namedtuple`的属性和方法

         1. `_fields`：得到它的fields tuple
         2. `_make()`重新载入数据
         3. `_asdict()`：将namedtuple变成`(field name, value)`的字典

      ##### slicing

      1. 所有的sequence都支持**slicing**

      1. 为什么所有的语言中，slicing和range都不包括最后一个对象
         1. 所有语言都是从0开始数，所以不包括最后一个的话，就可以很好地知道sequence的长度，比如`range(3)`就能知道长度为3
         2. 给了开始和结尾位置后，就能直接通过`end - start`来知道长度
         3. 可以从位置x分开一个sequence，而不用担心分开后有重合的部分
      2. `[a:b:c]`表示从a到b，以step c。
         1. 这个表示方法只能在`[]`中
         2. 这个方法会生成一个slice object：`slice(a,b,c)`，所以`seq[a:b:c]`实际上会调用特殊方法`seq.__getitem__(slice(a,b,c))`
         3. 会生成对象表示我们可以命名一个经常使用的slice对象，然后在需要的时候再传入`[]`中
      3. `[]`操作符还可以传入多个multiple indexes，用逗号隔开，这个特性在`Numpy`中使用二位数组时使用过
      4. 省略号`...`同样能被Python识别。会被识别为一个`Ellipsis`对象，可以在slicing中使用，比如：`x[i, ...]`就相当于`x[i, :, :, :,]`

   ##### Sequences使用*和+

   1. 两个运算符都是返回新的对象，而不会改变原来的对象

##### Building lists of lists

1. 尽量避免对元素是list的list使用乘法，因为list是reference，所以会造成生成的其他list其实也是同样的ref，所以改变一个list中的值的时候，其他的元素中响应的元素都会改变

   ```python
   right = [['_'] * 3 for i in range(3)] // 对的
   wrong = [['_'] * 3] * 3 //错的
   ```

2. 但是如果元素不是list之类的ref，就没事，这主要还是Python的拷贝机制导致的

##### 对sequence进行加强型的赋值

1. 也就是`+=`和`*=`，这些是特殊方法`__iadd__和__imul__`，如果这些方法没有设置，就会调用对应的`__add__和__mul__`
2. 对于mutable sequence，使用*=和+=后，原对象的位置未变，只是在后面附加上了一些元素，但是对于imutable sequence，则是会指向不同的对象
3. 可以使用`dis.dis('expression')`来查看详细的运行方式

##### list.sort还有内置的sorted函数

1. `list.sort`是sort in place，也就是不拷贝，返回None
   1. 会更加方便
2. 内置的sorted函数会创造新的list，而且会返回它
   1. 可以对immutable iterable使用，因为不会改变传入的sequence
3. 两者都有2个可选的，keyword-only参数
   1. reverse：默认False，如果是True，就倒序排序
   2. key：用来生成排序的key

##### 用bisect来管理ordered sequences

1. `bitsect`模块提供了2个主要函数：`insect和insort`，用来快速找到和插入元素到一个已经排序好的sequence中
2. `bitsect(haystack, needle)`
   1. 会在haystack中利用二分法寻找needle
   2. 会返回index，可以用于`haystack.insert(index, needle)`中，作为index参数
   3. 有两种调整的方法
      1. 有一对可选的参数：`lo`，`hi`，可以让我们缩小搜索的范围，`lo`默认是0，`hi`默认是sequence的最后一个
      2. 还有一个函数叫`bisect_left`，也就是当搜索到与needle相同的值时，`bitsect`会将needle插入到其右边，而`bisect_left`则会将其插入到左侧
3. `insort`
   1. 但是使用`insort`就可以完成`bitsect`的2中的两个步骤，而且执行起来更快
   2. 与上面一样，它也有`lo`和`hi`两个可选参数
   3. 也有`insort_left`这个姐妹函数

##### 当一个list并不是答案时

1. 有时list并不足够高效，所以我们希望返回其他类型，比如当返回一百万个浮点数时，使用array会比使用list要高效得多

2. 如果要经常检查一个元素是不是在sequence当中，那么使用set是一个很好的选择

3. Arrays

   1. 如果存入的数据类型都相同，那么使用array是一个很好的选择

   2. arrays提供所有的mutable sequence所提供的操作：`pop(), insert(), extend`等等

   3. 而且arrays还提供了快速存储和载入的`.frombytes`和`tofile`方法

   4. 使用示例

      ```python
      from array import array //要从array模块中导入
      from random import random
      
      //生成array
      floats = array('d', (random() for i in range(10**7)))
      //将array存储到文件
      fp = open('floats.bin', 'wb')
      floats.tofile(fp)
      fp.close()
      //将文件数据载入array
      floats2 = array('d')
      fp = open('floats.bin', 'rb')
      floats2.fromfile(fp, 10**7)
      fp.close()
      ```

   5. 使用array的方法存储和载入bytes文件非常搞笑，不仅载入和存储的速度比txt文件快得多，而且生成的文件也比文本文件小得多

   6. 还有一种存储数值数据的方法是使用**pickle**模块。pickle模块支持所有的内置类型，而且速度和array的内置方法差不多

4. Memory views

   1. 是一个内置的`memoryview`的类

   2. 他是一种shared-memory的sequence类型，可以让我们在不拷贝的情况下处理arrays的切片

   3. 这对处理大数据集非常重要，因为它不会先拷贝数据，所以能剩下大量时间

   4. 有点像一个修饰器，使用方法：

      ```python
      numbers = array.array('h', [1, 2, 3, 4, 5])
      memv = memoryview(numbers)
      memv_oct = memv.cast('B')
      memv_oct.tolist()
      ```

5. Numpy和Scipy

   1. 这两个库主要是用C写的，所以既有C的高效，也有Python方便的API

6. Deques和其他的队列

   1. 在`collections`模块中

   

#### 字典和集合

##### Generic Mapping types

1. Python中的dicts使用了hash table，所以非常高效

2. 下面是继承关系图![fluentpy2](/home/lzh/Pictures/fluentpy2.png)

3. 2中可以看到，`collections.abc`模块提供了`mapping和mutablemapping`两个类型，来为dict提供接口

4. 我们在使用的使用会扩展`dict`或者`collections.UserDict`，而不会使用这些ABCs。这些ABCs的意义在于规定接口，以及作为`isinstance`函数的标准：`isinstance(my_dict, abc.Mapping)`

5. 所有标准库的映射类型都使用`dict`作为具体实现，所以都有一个限制，那就是key必须是hashable

6. 一个对象是hashable的标准

   1. it has a hash value which never changes during the lifetime。这里要注意：
      1. has a：表示并不要求对象中所有数值都要这样，只要求有一个数值能满足这些条件
      2. 需要`__hash__()`特殊方法
   2. can be compared to other objects
      1. 需要`__eq__()`特殊方法
   3. hashable objects which compare equal must have the same hash value

7. 创建`dict`

   ```python
   a = dict(one=1, two=2, three=3)
   b = {'one': 1, 'two': 2, 'three': 3}
   c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
   d = dict([('two', 2), ('one', 1), ('three', 3)])
   ```

##### 通用的Mapping方法综述

1. 比较常见的方法![fluentpy3](/home/lzh/Pictures/fluentpy3.png)
2. 其中值得注意的
   1. update方法是一个典型的duck type的应用示例：
      1. 会检查m是否有`keys()`这个方法，如果有的话，会假设他是一个mapping
      2. 不然的话，会遍历m，然后假设它的item是(key, value)对
   2. `setdefault(k, [defalut])`
      1. 可以解决`get(key, default)`需要很多步骤才能解决的事。
      2. get方法只能返回默认值，如果我们想将缺失值的(key, value)对插入到字典中，则需要好几步
      3. 而setdefault()则不是，当遇到缺失值时，会返回默认值，并将缺失值的(key, value)对插入到字典中，如果默认值是list这种，还可以在这个基础上改变value的值

##### Mapping with Flexible Key Lookup

1. 有时候字典中会有缺失值，有几种方法来处理这个情况：
   1. 使用`defaultdict`
   2. subclass `dict`，添加特殊方法`__missing__`
2. `defaultdict`
   1. 实例化defaultdict的时候，需要传入一个callable用来得到默认值，比如可以传入`list`
   2. 当查询缺失值时，会有以下几个步骤
      1. 使用callable来创造新的值
      2. 将(key, value)对插入到字典中
      3. 返回这个值的ref
   3. 传入的callable因为会生成默认值，所以叫做`default_factory`
   4. default_factory只有在使用`__getitem__`这个特殊方法时才会被调用，使用其他方法则不会，比如`dict.get(k)`实际上调用的是`get`方法，所以仍然会返回None，而不会调用默认工厂
3. The `__missing__`方法
   1. 同样的，也是只有在`__getitem__`方法遇到问题时才会使用`__missing__`方法。
   2. 最好使用`collections.UserDict`的子类，而不是内置字典的，因为：

##### dict的变种

1. `collections.OrderedDict`

   1. 将item按照插入顺序排放

2. `collections.ChainMap`

3. `collections.Counter`

   ```python
   ct = collections.Counter('adfaftfgf')
   ct   ->    Counter({'a':2, 'f': 4, 'd':1, 't': 1, 'g': 1})
   
   //再记录一些
   ct.update('aaaaf')
   ct   ->    Counter({'a': 6, 'f': , 'd':1, 't': 1, 'g': 1})
   
   //访问属性
   ct.most_common(2)  ->  [('a', 6), ('f', 5)]
   ```

4. `collections.UserDict`

   1. 工作起来和dict一样

##### Subclassing UserDict

1. 写我们的新类时，继承Userdict而不是内置的dict的主要原因是：
   1. 看不太懂
2. UserDict是`MutableMapping`的子类

##### Immutable Mappings

1. `type`模块提供了包装器类叫做`MappingProxyType`，传入mapping类型后，会返回一个read-only but dynamic view of the origin mapping
2. 因为返回的是一个view，前面提到view是共享内存的，所以如果改变原来的mapping对象，会体现到返回的mapping上面

##### Set Theory

1. set是一系列不同值的集合，所以最基本的用处就是用来去掉重复的值
2. set的元素必须是hashable的，但是set本身不是hashable。而frozenset是hashable，所以set的元素可以是frozenset
3. set支持几种操作符：
   1. `a | b`返回union
   2. `a & b`返回交集
   3. `a - b`返回difference
4. set的写法写作`{elements}`，但是空集不能写作`{}`，这种表示的是空字典。要写作空集，应该写成`set()`
5. 同样可以使用类似listcomp来构造set
6. set的架构图![fluentpy4](/home/lzh/Pictures/fluentpy4.png)

##### dict and set Under the Hood

1. hash table

   1. hash table是一个sparse array，所以是可以通过offset来利用时间O(1)来访问到相应的item
   2. Python尽量保持至少三分之一的buckets是空的
   3. 加入item到哈希表中，首先要算哈希值（`hash()`内置函数）
   4. 为了让哈希表更加有效，应该让那些不相等但是相近的值的哈希值相差很大，其实就是让整个哈希表都均匀地分布着item
   5. 哈希表有时会加上salt value，以防止DOS攻击，这些在相应的文件中都会有写

2. 哈希表的算法![fluentpy5](/home/lzh/Pictures/fluentpy5.png)

3. 使用哈希表的后果

   1. `keys`必须是可哈希的

      1. 满足下面三个要求，就是可哈希的
         1. 支持`hash()`函数，而且在lifetime中，它们的哈希值都不变
         2. 支持比较，也就是`__eq__`特殊方法
         3. 如果`a==b`，那么`hash(a)`也必须等于`hash(b)`
      2. 自定义的类型也是默认可哈希的，因为它们的哈希值可以用`id()`而且它们比较起来也各不相同
      3. 自定义类型中，如果自定义`__eq__`方法，必须也要定义合适的`__hash__`，因为上面提到的可哈希的第三个条件

   2. dicts的存储空间占用会相对比较大

      1. 因为哈希表是稀疏的，所以在空间使用方面不够有效率
      2. 所以当处理很多数据时，最好把他们存储为tuples，而不是字典，因为在两方面降低了存储的使用
         1. 去掉了哈希表
         2. 不用给每个record存储field names
      3. 对于自定义的类型，可以使用`__slots__`属性来改变示例属性的存储方式，在第9章会提到

   3. key search会非常快

   4. key的顺序取决于插入的顺序

      1. 其实就是前面讲到的hash collision的存在

   5. 加入新的item可能会改变已存在的key的顺序

      

#### Text与Bytes

##### Character Issues

1. 



### 函数

#### First-Class 函数

#### First-Class 函数的设计步骤

#### 函数装饰器和终止







### 面对对象

#### Object References, Mutability, Recycling

#### Pythonic的对象

#### 序列Hacking, Hashing和切片

#### 接口：从协议到ABCs

#### 继承

#### 操作符号重载









### 控制流

#### 遍历，遍历器和生成器

#### 上下文管理器和else Blocks

#### 协程

#### 并发

#### 用asyncio实现并发









### 元编程

#### Dynamic Attributes和 Properties

#### 属性描述

#### 类的元编程

