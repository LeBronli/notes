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

1. 每个character都对应着一个`code point`，由几部分组成
   1. 以`U+`开头，表示是Unicode
   2. 后面跟4-6个16进制的数字
   3. 比如`A=U+20AC`
2. 实际上的character的bytes数值取决于encoding的方式，也就是将code point变成bytes编码的方式
   1. 比如说A使用UTF-8编码后，由bytes`\x41`表示，用UTF-16LE编码后，由`\x41\x00`表示
3. `string`可以直接使用`encode`和`decode`方法来转变编码方式
4. Python中有2种内置的二进制sequence，分别是
   1. immutable `bytes`
   2. mutable `bytearray`
5. 在`bytes`和`bytearray`中：
   1. 每个item都是一个0-255的整数，用十六进制来表示，比如：`\xc3`
   2. 取单独的一个item时，会返回整数，比如`cafe[0] = 99`
   3. 但是如果取slicing，依然返回原类型，比如`cafe`是bytes，那么`cafe[:1] = b'c'`，也就是切片后仍然是bytes
   4. 虽然这些二进制的序列都是由整数组成，但是`literal notation`，也就是打印出来反应的，却会把ASCII嵌在里面，只有当无法用ASCII表示出来，才会使用十六进制表示，比如café在encode之后，变成了`b'caf\xc3\xa9'`，但是这只是看起来这样，实际上还是一系列的整数组成的，每个元素是由8个组成，也就是一个byte。
   5. 这两种类型都支持绝大多数除了`format`和`format_map`之外的string使用的方法
   6. 这两种二进制序列有一个方法是string没有的，那就是`fromhex`，可以处理一系列十六进制的由空格分开的字符，成为二进制序列：`bytes.fromhex('31 4B CE A9')`会生成相应的二进制序列
6. 构建二进制序列的其他方法：使用constructors，传入下面的参数
   1. 字符串和encoding所用的方式
   2. `iterable`提供0-255的item
   3. 一个整数，来创造一个二进制序列
   4. 执行`buffer protocol`的对象
7. `struct`模块提供了函数来将packed bytes处理为a tuple of fields
8. 如果经常读写二进制文件，可以学习`mmap`也就是memory-mapped file

##### 理解编码和解码问题

1. 如果在encoding的过程中，有一个不能被编码，就会触发`UnicodeEncodeError`，反过来就是`UnicodeDecodeError`，为了解决这类问题，可以在encode时传入errors`参数，有几种可以传入：
   1. `'ignore'`
   2. `'replace'`，遇到无法处理的字符，用`'?'`代替
   3. `'xmlcharrefreplace'`遇到无法处理的字符，用一个XML entity来代替
2. decode时也可以传入`errors`参数：
   1. `'replace'`无法处理的用特殊的问号代替（code point `U+FFFD`）
3. `UTF-8`是Python3默认的encoding编码
4. 如果载入一个Python模块，但是其中包含非utf8编码的数据，如果没有提前进行encoding declaration，就会报错`SyntaxError`
5. 如果发现byte sequence的编码方式
   1. 一些文件和协议，比如HTTP和XML，在header中会告诉你编码方式
   2. 可以使用一个包：`chardetect`来识别编码方式
6. BOM
   1. 使用UTF-16编码的时候，会发现前面会多加一些bytes，这是用来表示`endian`方式的，会告诉CPU如何工作
   2. 当是little endian的时候，会从左到右，不然就是从右到左
   3. 这里感觉有点乱

##### 处理文本文件

1. 处理文本的时候使用`str`，只有在最后输出的时候才将其转换成`bytes`

2. 虽然使用文本文件很简单，但是还是要注意编码问题：

   ```python
   open('cafe.txt', 'w', encoding='utf_8').write('café')
   open('cafe.txt').read()
   ```

   1. 这样得到的和写入的并不一样
   2. 因为在读的时候没有指定解码方式，在这种情况下，会默认使用操作系统的默认encoding，如果是Windows，就会使用Windows1252，就会导致得到的和输入的不一致，但是如果用MacOS或者Linux，则表现正常
   3. 为了以防万一，也为了能够跨平台，我们读和写的时候都要显式地指定编码方式

3. 不要用二进制方法打开文本文件，除非你需要分析文本文件的编码方式

4. `eval`可以将字符串变成代码执行

5. 因为编码的默认方式等原因，Windows上面更容易面对编码问题

##### Normalizing Unicode for Saner Comparisons

1. 对于两个字符串，虽然编码看起来不一样，但是它们打印出来是一样的：分别是`'café'和'cafe\u0301'`
   1. 因为`U+0301`是COMBINING ACUTE ACCENT，把他加在`e`后面，就会变成法语的`é`
   2. 但是这两个字符是不相等的，因为长度不一样
   3. 解决方法就是使用**Unicode normalization**。由函数`unicodedata.normalize`来提供
      1. 第一个参数是下面四个字符串中的一个
         1. `NFC`
         2. `NFD`
         3. `NFKC`
         4. `NFKD`
2. `str.casefold()`
   1. 如果里面的字母全部都是`latin1`字母集中的，和使用`str.lower()`的结果相同，除了2个例外
      1. 希腊字母`mu`
      2. sharp s
3. 去掉变音符号
   1. 首先将characters分解成基本字符：`norm_txt = unicodedata.normalize('NFD', txt)`
   2. 筛选出其中的基本字符（去掉连接字符）：`''.join(c for c in norm_txt if not unicodedata.combining(c))`

##### Sorting Unicode Text

1. 

##### Dual-Mode str and bytes API

1. 使用正则表达式时：
   1. 如果对bytes使用，`\d和\w`只匹配ASCII字母集
   2. 如果对str使用，`\d和\w`能够匹配数字和字母



### 函数

#### First-Class 函数

##### 像看待对象一样看待函数

1. 可以直接将函数名作为参数传入
2. 也可以直接在函数名后面加括号当成函数使用

##### Higher-Order Functions

1. 一种接受函数作为参数并且返回函数的函数，叫做**higher-order function**
2. 任何只有一个参数的函数都可以作为参数被传入函数
3. 一些最经典的高级函数：`map, filter, reduce, apply`，但是`apply`在Python3中被移除了，因为不再必要，可以使用`fn(*args, **keywords)`来代替`apply(fn, args, kwargs)`，其他的函数虽然还在，但是也经常有更好的选择
4. 随着listcomp和genexp的出现，它们可以以一种更加可读的方式完成map和filter的工作
5. Python3中，`map`和`filter`返回生成器
6. `reduce`函数在`functools`模块中，它最常见的使用场景，也就是summation，可以被`sum`内置函数更好地完成
7. 其他的reducing built-ins：
   1. `all(iterable)`
   2. `any(iterable)`
8. 为了使用高阶函数，很多时候需要创造那种很小的，用完即弃的小函数，这也是匿名函数存在的原因
9. 匿名函数
   1. 通过`lambda`关键字创造
   2. lambda函数中，不能出现：
      1. 赋值
      2. 使用其他Python语句，像while, try等等
   3. 在高阶函数之外，匿名函数的使用场景很有限
   4. lambda只是一个语法糖，lambda表达式和def一样创造了一个函数对象
10. 可以通过`callable()`这个内置函数，来判断一个对象是不是callable
11. Python的文档列出了下面7中callable object
    1. 用户定义的函数
    2. 内置函数
    3. 内置方法
    4. 用户自己定义的方法
    5. 类
    6. 类的实例
    7. Generator functions，也就是使用yield而不是return来返回的函数，当调用时，会返回生成器对象。比较特殊，后面还可以用作协程
12. 用户定义的可调用类型
    1. 任何对象，只要定义了`__call__`特殊方法，也可以和函数一样被调用
    2. 一种这种的应用是装饰器。
    3. 有一种与创造函数完全不同的就是使用`closure`
13. 函数 introspection
    1. 和普通的用户定义的类一样，函数也使用`__dict__`属性来存储用户赋予它的属性
    2. 找到两个对象属性的区别，最好的就是使用`set(dir(obj1)) - set(dir(obj2))`
    3. 函数还有几个重要的特殊方法可以被IDE和框架利用，来获得函数签名的信息：
       1. `__defaults__`
       2. `__code__`
       3. `__annotations__`
14. 函数的参数：从位置参数到keyword-only参数
    1. 有一个非常好用的点在于：可以用*和**来拆开iterables和mappings
    2. keyword-only 参数是一个Python3中的新特性。为了定义它，我们把它放在*和**之间，这样就没办法用positoinal参数了，因为如果没有指定关键词，会被前面的\*给抢走
    3. keyword-only参数没有必要一定要有默认值
15. 装饰器可以通过方法了解到被装饰的函数所需要的参数
    1. 函数的`__defaults__`属性是一个tuple，里面保存了所有参数的默认值
    2. 函数的`__kwdefaults__`属性保存keyword-only参数的默认值
    3. `__code__`属性可以得到函数内变量
       1. 得到的是一个对象，如果想要得到详细的变量名，要使用`obj.__code__.co_varnames`，会得到tuples，里面的元素是变量名，前N个是参数名，N可以通过2中方法得到
       2. 使用`obj.__code__.co_argcount`得到参数的数量
16. 15中的东西我们可以利用另一种方法更加清晰地得到：
    1. 从`inspect`模块导入signature函数
    2. `signature`函数会返回`inspect.Signature`对象
    3. 对这个对象使用`str()`函数就可以得到包含参数的tuple
    4. 对象的`parameters`属性会返回一个字典，元素为`(name, param)`
    5. `param`对象可以通过`default`属性得到默认值
    6. `param`对象还可以通过`kind`属性得到是位置参数还是keyword-only参数，有几种：
       1. `POSITIONAL_OR_KEYWORD`
       2. `VAR_POSITIONAL`：使用`*args`表示的
       3. `VAR_KEYWORD`使用`**kwargs`表示的
       4. `POSITIONAL_ONLY`
    7. `Signature`对象还有`bind`方法，可以接受任意数量的参数，然后将他们绑定到参数里面，这经常在框架中被用于validate arguments prior to the actual function invocation，C++中也有一样的东西
17. 函数注解
    1. Python3可以为函数的参数和返回值添加注解
    2. 如果有默认值，注释在两者中间：`def clip(text:str, max_len:'int > 0' =80) -> str`
    3. 不会为了注解做什么处理，它们只是存储在`__annotations__`属性中，是一个dict，key为参数和返回值名字，value为它们的注释
    4. 我们同样可以通过`inspect.Signature`对象来处理注解：
       1. 它的`.return_annotation`属性
       2. 它的`.parameters.values()`方法

##### 函数变成的包

1. `operator`模块
   1. 里面有各种基本运算操作，比如说`operator.mul`，这是一个可调用的，可以用于`reduce`函数中代替自己写的`lambda`函数
   2. 还有一组函数，用来遍历sequence，或者获取对象的属性：`sorted(one_list, key=itemgetter(1))`，这个就将这个list中对象的第一个部分作为排序的key
      1. 还有`attrgetter`：`sorted(iterables, key=attrgetter('coord.lat'))`
      2. 还有`methodcaller`：`sorted(iterables, key=methodcaller('upper'))`
   3. 如果想要知道一个包内提供的函数，就可以对包的名字使用`dir`函数
   
2. functools模块提供的高阶函数

   1. `reduce`：但是前面说了，可以被更好的函数`sum`所替代

   2. `partial`，以及它的变种`partialmethod`：是现在这个包内最有用的函数了。

      1. 可以接受一个函数，然后将其中的部分参数固定后再返回这个函数

      2. 比如：

         ```python
         triple = functools.partial(mul, 3)
         triple(7)
         ```

      3. 接受callables作为第一个参数，后面紧跟着任意多个位置和关键字参数

#### First-Class 函数的设计步骤

1. 

#### 函数装饰器和终止

1. 装饰器是一个把另一个函数作为参数的callable

2. 装饰器严格来说只是语法糖，但是在做元编程的时候，装饰器的存在确实会让其非常方便

3. 装饰器在被import时就会运行，我们把这个叫`import time`，而被装饰的函数在运行代码时才会运行，我们叫`runtime`

4. 因为装饰器在`import time`的时候就会运行，所以非必要不要在装饰器中打印消息，因为这样的话，会在运行自己的代码前就打印出消息，而且如果import这个文件，也会打印出消息，这不是我们想要的

5. variable scope的一个例子：

   ```python
   b = 6
   def f2(a):
       print(a)
       print(b)
       b = 9
       
   f2(3)
   ```

   1. 这个例子中，打印b时会报错
   2. 看起来很奇怪，但其实是因为Python的编译问题：因为Python看到b在函数体内被赋值，所以觉得它是`local variable`，所以Python会尝试从local environment中取b。
   3. 为了避免这种情况，可以在尝试使用全局变量的时候，在一开始用像`global b`这样的命令注明

6. **Closures**

   1. 定义：

      1. a function
      2. with an extended scope that encompass nonglobal variables referenced
      3. in the body of the function
      4. but not defined there

   2. 示例

      ```python
      def make_averager():
          series = []
          
          def averager(new_value):
              series.append(new_value)
              total = sum(series)
              return total/len(series)
          
          return averager
      ```

   3. 其实就是定义一个工厂函数，返回一个可调用对象。而工厂函数中定义了一个局部变量，它返回的可调用对象操作那个局部变量

   4. 其实到现在我还没有懂这样的必要，完全可以创建一个对象，然后使用这个对象的方法来操作就行了把？

   5. 可以通过`__code__`属性来查看返回的可调用对象的变量

      1. `.__code__.co_varnames`属性是可调用对象的参数和局部变量
      2. `.__code__.co_freevars`属性是其可以操作的那个变量

   6. 还可以通过`__closure__`属性来访问返回的对象操作的那个变量

      1. 直接使用`.__closure__`返回那个对象
      2. 可以使用`.__closure__[0].cell_contents`来得到变量中的值

7. `nonlocal`声明

   1. 前面闭包的那个示例不够高效，因为我们只想知道平均值，没必要存储整个序列，所以可以改成这样：

      ```python
      def make_averager():
          count = 0
          total = 0
          
          def averager(new_value):
              count += 1
              total += new_value
              return total/count
          
          return averager
      ```

   2. 但是这样执行后会报错，因为我们前面5中提到的那个原因，count与total的+=操作会让Python认为它们是局部变量，所以会尝试从局部变量中寻找，从而报错

   3. 但是我们不能使用5中的解决方法，即使用`global`，因为这些变量同样也是函数中的

   4. 这是我们就可以使用`nonlocal`声明，在`averager`函数中，首先使用`nonlocal count, total`语句

##### 写一个简单的装饰器

1. 因为装饰器是在`import time`执行的，所以：

   ```python
   @clock
   def factorial(n):
       return 1 if n < 2 else n*factorial(n-1)
   
   //实际上是这样的
   def factorial(n):
       return 1 if n < 2 else n*factorial(n-1)
   factorial = clock(factorial)
   ```

2. 1中的装饰器带来了一些问题：

   1. 不支持关键词参数

   2. 覆盖了原函数的`__name__和__doc__`属性

   3. 可以使用`functools.wraps`这个修饰器来改进

      1. 这样包裹住之后，就可以得到这个函数的相关信息了，这样就可以在下面进行修改

      ```python
      import functools
      
      def clock(func):
          @functools.wraps(func)
          def clocked(*args, **kwargs):
              t0 = time.time()
              result = func(*args, **kwargs)
              elapsed = time.time() - t0
              name = func.__name__
              arg_lst = []
              if arg:
                  arg_lst.append(','.join(repr(arg) for arg in args))
              if kwargs:
                  pairs = ['%s=%r' % (k, w) for k, w in sorted(kwargs.items())]
                  arg_lst.append(','.join(pairs))
              arg_str = ','.join(arg_lst)
              print()
              return result
          return clocked
      ```

##### 标准库中的装饰器

1. Python有3个内置的函数用来装饰方法：

   1. `property`
   2. `classmethod`
   3. `staticmethod`

2. 还有一个常用的是`functools.wrap(func)`，用来帮助建造好用的装饰器

3. 标准库`functools`中还有2个又去的装饰器，分别是：

   1. `lru_cache`

      1. 存储前面调用的结果，避免重复运算那些用过的参数

      2. `lru`表示least recently used

      3. 缓存是有限的，随着存储数量的增加，会丢弃掉以前不用的

      4. 示例，用这个来解决迭代的重复计算问题：

         ```python
         import functools
         
         @functools.lru_cache()
         def fibonacci(n):
             if n < 2:
                 return n
             return fibonacci(n-2) + fibonacci(n-1)
         
         fibonacci(6)
         ```

      5. 完整的签名：`functools.lru_cache(maxsize=128, typed=False)`

         1. 为了优化考虑，`maxsize`应该设为2的n次方
         2. 如果`typed`设为True，那么会让那些不同类型的值直接判定为不一样，比如`1 != 1.0`，如果是False，那就可以知道他们一样

      6. 它用`dict`来存储结果，它的key由位置和关键词参数组成，所以必须是hashable的

   2. `singledispathch`

      1. 是用来解决Python中没有重载函数的问题的

      2. 使用这个装饰器之后，函数会变成`generic function`，也就是一组函数以不同的方式完成相同的操作，取决与第一个参数

      3. 使用示例：

         ```python
         from functools import singledispatch
         from collections import abc
         import html
         import number
         
         @singledispatch	            //用来标记base function
         def htmlize(obj):          
             content = html.escape(repr(obj))
             return '<pre>{}</pre>'.format(content)
         
         @htmlize.register(str)     //用来注册其他类型
         def _(text):              //因为这个名字无关紧要，所以使用'_'作函数名是最好的
             content = html.escape(text).replace('\n', '<br>\n')
             return '<p>{0}</p>'.format(content)
         
         @htmlize.register(numbers.Integral)
         def _(n):
             return '<pre>{0} (0x{0:x})</pre>'.format(n)
         
         @htmlize.register(tuple)
         @htmlize.register(abc.MutableSequence)
         def _(seq):
             inner = '</li>\n<li>'.join(htmlize(item) for item in seq)
             return '<ul>\n<li>' + inner + '</li>\n</ul>'
         ```

      4. 使用`abc`来表示类可以让我们想要的类更加多

##### Parameterized 装饰器

1. 通过写一个装饰器工厂（返回装饰器的函数），来接受其他参数。因为装饰器本身是只能接受被装饰的函数名这一个参数的





### 面对对象

#### Object References, Mutability, Recycling

##### 变量不是盒子

1. 变量是标签，而不是盒子
2. 对于reference object，说变量被赋值给对象才会更合理一些，因为对象比赋值操作来的更早

##### Idnetity, Equality and Aliases

1. 区分`==`和`is`
   1. `==`只是看值相不相等
   2. `is`则是看两个对象是不是同一个
2. 当比较变量和singleton，最好使用`is`，因为它操作起来更快，因为他不会被重载
   1. `==`实际上是使用`a.__eq__(b)`的语法糖
   2. 而`is`可以直接比较它们的id，执行更快
3. 数组的相对不可变性
   1. 数组的不可变指的是数组中的元素，但是如果元素是reference的话，这个reference指向的数据是可以改变的
   2. `t1 = (1, 2, [30, 40])`
   3. 但是我们不能调用`t1[0] = 10`，因为数组的元素不可变
   4. 但是我们可以调用`t1[-1].append(50)`，因为list元素存储的只是reference

##### Copies are shallow by default

1. 常见的2种拷贝方法都是浅拷贝

   1. `l2 = list(l1)`即使用建造函数
   2. `l2 = l1[:]`

2. 要注意上面两种拷贝和直接使用赋值的区别：

   ```python
   l1 = [1, 2, (3, 4), [5, 6]]
   l2 = l1
   l3 = l1[:]
   ```

   1. 在`l1[2].append(7)`语句之后，l2和l3都会变成`[1, 2, (3, 4), [5, 6, 7]]`
   2. 但是在`l1[0] = 10`语句之后，l3不会变，而l2则会变得和l1一模一样
   3. 上面的原因在于：
      1. 1中的2中虽然是浅拷贝，但是实际上将l1中的所有元素都拷贝了一遍，如图所示（图中l2就是上面的l3）![fluentpy6](/home/lzh/Pictures/fluentpy6.png)
      2. 所以浅拷贝之后，原list改变普通元素不会改变拷贝得到的l3，只有改变了元素指向的list，拷贝后的l3才会跟着改变，因为它们的第3个元素都指向同一个底层数据![](/home/lzh/Pictures/fluentpy6.png
      3. 而l2这种其实并不能叫拷贝，而是为原list取了一个别名，任何时刻与原list都是一模一样的

3. `copy`模块提供了：

   1. `copy()`函数提供浅拷贝
   2. `deepcopy()`函数提供深拷贝

##### Function Parameters as References

1. Python的参数传递策略

   1. 当类型为`int`等的时候，拷贝值传递，也就是函数内的改动不会影响到函数的参数变量
   2. 当类型为`list`等的时候，拷贝ref传递，函数内会影响到函数外

2. 应当避免mutable object as a default values for parameters

   1. 示例：

      ```python
      class HauntedBus:
          """A bus model haunted by ghost passengers"""
          def __init__(self, passengers=[]):
              self.passengers = passengers
          def pick(self, name):
              self.passengers.append(name)
          def drop(self, name):
              self.passengers.remove(name)
      ```

   2. 这个对象的passengers使用了默认值，我们看到下面的应用就出现了问题：

      ```python
      >>> bus2 = HauntedBus()
      >>> bus2.pick('Carrie')
      >>> bus2.passengers
      ['Carrie']
      >>> bus3 = HauntedBus()
      >>> bus3.passengers
      ['Carrie']
      >>> bus3.pick('Dave')
      >>> bus2.passengers
      ['Carrie', 'Dave']
      >>> bus2.passengers is bus3.passengers
      True
      >>> bus1.passengers
      ['Bill', 'Charlie']
      ```

   3. 归根结底，就是默认值的那个空list，它对于所有的调用的对象都是一样的：

      1. 在初始化函数中加上一行代码，打印出空list的id：`print(id(passengers))`

      ```python
      >>> bus2 = HauntedBus()
      139798537870464
      >>> bus2.pick('Carrie')
      >>> bus2.passengers
      ['Carrie']
      >>> bus3 = HauntedBus()
      139798537870464
      >>> bus3.passengers
      ['Carrie']
      >>> bus3.pick('Dave')
      >>> bus2.passengers
      ['Carrie', 'Dave']
      >>> bus2.passengers is bus3.passengers
      True
      >>> bus1.passengers
      ['Bill', 'Charlie']
      ```

   4. 为了应对这种情况，一般会将默认值设为`None`，然后再在接下来的代码里面进行修改

3. 有时我们不希望函数改变传入的参数，这时，我们就需要进行`copy`

   1. 对于`iterable`，可以直接使用`list(iterable)`，这样既可以用构造函数复制，又可以扩大参数的范围，因为`list`可以接受所有的iterable

4. `del`和垃圾收集

   1. `del`只是删除名字，而不是对象。
   2. 它有可能会造成对象被删除，但是这是间接导致的，因为名字被删除，其指向的对象引用数归零时，被自动清除了
   3. `__del__`特殊方法在对象要被摧毁的时候被调用，用来释放外部的资源
      1. 我们不应该在自己的代码里面使用这个特殊代码
   4. 在CPython中，最初的垃圾回收算法是计算ref
   5. 后来在CPython2.0中，使用了新的算法

##### 弱引用

1. 弱引用不增加对象的引用计数

2. 被引用所指向的对象叫做`referent`

3. 弱引用在缓存中很有用

4. 当我们想要细微地管理存储的时候，经常被隐式的赋值所影响，比如

   1. _ console variable

      1. 就是在console中被打印出来的那个变量，比如下面的例子：

         ```python
         >>> import weakref
         >>> a_set = {0, 1}
         >>> wref = weakref.ref(a_set)
         >>> wref
         <weakref at 0x100637598; to 'set' at 0x100636748>
         >>> wref()
         {0, 1}
         >>> a_set = {2, 3, 4}
         >>> wref()
         {0, 1}
         >>> wref() is None
         False
         >>> wref() is None
         True
         ```

      2. 这里在`wref() is None`这个语句之后，`{0, 1}`这个对象就被回收了，因为这时console的`_`变量指向的是False，而不再是`{0, 1}`

   2. Traceback objects

5. `WeakValueDictionary`

   1. 是一个mutable mapping

   2. values是weak references to objects

   3. 当指向的对象被回收了，含有弱引用的那一项也会被自动删除

   4. 经常在caching中被应用

   5. 下面是使用方法和一个不能忽视的错误

      ```python
      >>> import weakref
      >>> stock = weakref.WeakValueDictionary()
      >>> catalog = [Cheese('Red Leicester'), Cheese('Tilsit'),
      ...
      Cheese('Brie'), Cheese('Parmesan')]
      ...
      >>> for cheese in catalog:
      ...
      stock[cheese.kind] = cheese
      ...
      >>> sorted(stock.keys())
      ['Brie', 'Parmesan', 'Red Leicester', 'Tilsit']
      >>> del catalog
      >>> sorted(stock.keys())
      ['Parmesan']
      >>> del cheese
      >>> sorted(stock.keys())
      []
      ```

      1. 这里之所以在删除`catalog`后，弱引用并没有全部删除干净，是因为在Python中，for产生的cheese这个是全局变量，所以在删除`catalog`后，仍然还有`cheese`指向最后一个元素，也就是`Parmesan`，这也是为什么最后只剩他
      2. 要特别小心这种临时变量，很可能带来意想不到的后果

6. `weakref`模块还能提供`WeakKeyDictionary`和`WeakSet`

7. 如果想要建造一个知道所有其实例的类，可以使用`WeakSet`，因为如果使用正常的set，会造成实例无法被正常回收

8. 弱引用的限制

   1.  不能指向`list`和`dict`
   2. 但是可以用一个类将1中的包装起来，就可以指向了
   3. 可以直接指向用户定义的类
   4. 但是对于`int`和`tuple`实例，就算是用类包含了也不能指向
   5. 这些限制都是针对CPython的，是内部优化的结果

9. Python对于Immutables的一些小tricks

   1. 对于`tuple`，就是算是构造函数和`t[:]`，也是指向相同的底层数据，而不是进行复制
   2. 这样的现象对于`str, bytes, frozenset`这些immutables都是一样的（`fs[:]`对于frozenset不管用）
   3. sharing of string literals是一种优化技术，叫做`interning`。
   4. 3中的技术，CPython在int上也会做来避免非必要的重复那些常见的数字
   5. 所以对于`str和int`，多使用`==`而不是`is`，因为上面所讲的原因

#### Pythonic的对象

##### Object Representations

1. Python使用鸭子类型，就是不需要继承，只需要定义要求的那些方法就行了
2. 对象有两种表示方法：
   1. `repr()`：对应着特殊方法`__repr__`，展示开发者想看到的东西
   2. `str()`：对应着特殊方法`__str__`，展示用户想看到的东西
3. 还有两个特殊方法提供表示对象的选择
   1. `__bytes__`：与str类似，被`bytes()`函数调用，得到byte sequence
   2. `__format__`：内置的函数`format()`和`str.format()`都会调用它
4. 两种方法的装饰器
   1. `@classmethod`：在类上操作，而不是在类的实例上操作
      1. 所以传入的第一个参数是`cls`而不是`self`
      2. 主要使用场景是alternative constructors
   2. `@staticmethod`：让方法表现得像函数一样，可以在类和类的实例上都使用
      1. 不接受`cls`或者`self`
      2. 如果不想让方法和类产生交集，只是想把它定义在这个模块里，就可以使用这个
5. `format`函数
   1. 第一个参数是要规范化的对象
   2. 第二个参数是要规范化到的目标：`format(0.41514534, '0.4f')`
6. 如果没有定义`__format__`，就会使用`__str__`，但是这样就不能使用format specifier了
7. 定义`_format__`
   1. 示例：
   2. 第二个参数就是format specifier
   3. 可以使用string内置类型的format函数，可以更加简单地定义

##### Private and protected attributes

1. Python中并没有Java那种声明符，而只是简单的机制防止不小心重写了对象的属性
2. Python中所谓的`private`属性其实就是以两个下划线开头的属性名
3. Python会自动将以两个下划线开始的属性名字做处理：在前面加上一个下划线和类的名字：`__mood`会变成`_Dog__mood`
4. 以一个下划线开头的是`protected`属性，它对于Python解释器来说没有特别的含义。但是对于Python的程序员，我们知道不应该从外部访问以一个下划线开头的protected属性

##### 用`__slots__`属性来节省空间

1. Python一般将实例的属性存在实例的`__dict__`属性中
2. 这个属性是个dict，为了快速访问，使用了哈希表，所以也造成了很大的空间使用
3. 如果你的应用中有上百万个实例，使用这种方式来存储属性就有点问题了，这是可以使用`__slots__`属性来节省存储，感觉`namedtuple`比class节省内存也是因为这个
4. 原理是让Python将实例的属性存储为tuple而不是dict
5. 定义`__slots__`
   1. 首先创造这个属性
   2. 然后给他赋值an iterable of string with identifiers for the instance attributes
   3. 最好使用tuples，这样后面就不能改变了
6. 当处理非常多个实例的数值数据的时候，最好使用`numpy.ndarray`，这是优化非常好的
7. 不要在slots属性中存入dict属性，这样的话等于没做
8. `__weakref__`属性对于那些支持弱引用的对象来说是必须的
9. 如果既想要使用弱引用，有想要节省内存，可以将weakref存入到slots中
10. slots属性的问题
    1. 在每个子类中都必须重新声明`__slots__`，因为前面提到了，这个属性是不会继承的
    2. 实例中只会存储定义在slots中的属性，这也是为什么使用了slots可以节省空间：
       1. 只要不在slots中加入dict属性
       2. 实例中就不会有dict属性
       3. 这样的话，就可以节省很多空间
    3. 实例没法被弱引用指向，除非将`__weakref__`属性包含在slots属性中

##### 重载类的属性

1. 如果想要改类的属性，可以直接改，但是这样会影响到所有没有自己属性的实例

#### 序列Hacking, Hashing和切片

1. `reprlib.repr()`：这个函数可以输出安全的string，即当参数长度太大，可以省略后面部分

2. 可以使用`@property`装饰器来让属性只读：

   1. 其实就是将x定义为一个方法
   2. 将属性x加上下划线，这样就可以防止直接访问

   ```python
   class Vector 2d:
       typecode = 'd'
       
       def __init__(self, x, y):
           self.__x = float(x)
           self.__y = float(y)
           
       @property
       def x(self):
           return self.__x
   ```

3. 可以通过`cls.shortcut_names`属性来实现：

   ```python
   shortcut_names = 'xyzt'
   
   def __getattr__(self, name):
       cls = type(self)
       if len(name) == 1:
           pos = cls.shortcut_names.find(name)
           if 0<=pos< len(self._components):
               return self._components[pos]
       raise AttriteError()
   ```

4. 需要设置好`__setattr__`方法

   1. 因为如果没有设置好，可以通过`instance.property = value`来对属性进行更改，且不会报错

   2. 如何设置：

      ```python
      def __setattr__(self, name, value):
          cls = type(self)
          if len(name) == 1:
              if name in cls.shortcut_names:
                  error = 'read only attribute'
              elif name.islower():
                  error = 'can\'t set attributes from a to z'
              else:
                  error = ''
              if error:
                  raise AttritebuteError()
          super().__setattr__(name, value)
      ```

5. `itertools.zip_longest`可以zip很多个item，且可以接受`fillvalue`参数，这样就会自动用这个来代替缺失值

#### 接口：从协议到ABCs

##### Python中的接口和协议

1. 不推荐定义新的ABCs，过度定义的风险是很大的

2. 可以通过在类外定义函数，然后将函数名传入来在类外设置方法

   ```python
   def set_card(deck, position, card):
       deck._cards[position] = card
   
   RenchDeck.__setitem__ = set_card
   ```

3. 不应该过度使用`isinstance`

4. 

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

