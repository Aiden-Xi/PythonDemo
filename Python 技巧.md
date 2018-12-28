1. 快速反转：a = [1,2,3,4] b = 'abcdefg'
   ```
    a[::-1] ===> [4,3,2,1], b[::-1]===> 'gfedcba'
    正常的代码： list(reverse(a)),  list(reversed(b))
   ```
2. 使用with语句打开一个文件，可以避免文件读取失败时，文件未关闭的情况
    ```
    with open(path, 'r') as f:
        do_sth_with(f)
    ```
3. 格式化输出的时候可以使用空格占位符
    ```
    >>> dict = {'name': 'sdfsdf', 'age': 'ddd', 'sex': 'man'}
    >>> print(f"{dict['name']:10}-----dfk")
    >>> prrint("{0:10}----{1:8}" % (dict['name'], dict['age']))
    >>> sdfsdf    -----dfk
    ```

4. 在for 或是 while 循环中使用else的时候需要注意他的使用情况，
    - 在循环中使用了 break的，那么else不会被执行。
    - 如果循环的数据时空的，回立刻执行else语句
    - 如果初始循环的while语句是false，那么else也会立马执行

5. 了解如何在闭包里面使用外围作用域的变量
    ```
    可以使用关键字 nonlocal来进行注明：
    def sort_priority(nums, group):
        found = False
        def helper(x):
            nonlocal found
            if x in group:
                found = True
                return (0, x)
            return (1, x)

        nums.sort(key=helper)

        >>> 运行结果如果不添加nonlocal，found返回的结果是 False，因为他是闭包里面的变量（不可变的变量）相当于是在闭包里面新建来一个变量

        Python2 没有nonlocal关键字，但是可以使用list数据类型类实现该功能
        def sort_priority(nums, group):
            found = [False]
            def helper(x):
                if x in group:
                    found[0] = True
                    return (0, x)
                return (1, x)

        因为这里的found是一个可变对象（列表）满足符合条件

    ```

6. 设置方法默认值的时候，一定要使用None来设置，因为默认值只会在方法被加载的第一次里面调用，生成，后面调用方法的时候，其实是使用之前已经生成的默认值。这样回会出现很奇怪的问题的。
    ```
    def decode(data, default={}):
        try:
            return json.loads(data)
        except ValueError:
            return default

    boo = decode("boo")
    boo['boo'] = 'boo'
    far = decode('far')
    far['far'] = far

    print(boo)  ==>  {"boo": "boo", "far": "far"}
    print(far)  ==>  {"boo": "boo", "far": "far"}

    两次输出的结果是一样的，这是因为在方法加载的时候，返回的是同一个默认值。
    ```

7. 尽量使用辅助类来维护程序的状态，而不是使用字典或是元组

8. 使用函数充当API挂钩，在方法参数中，应该传递一个方法，而不是类的实例。

9. 可以定义新的类来记录函数的状态，并且新的类需要实现__call__方法。

10. 总是使用内置的 super函数 来初始化父类。

11. 使用mix-in 组建实现的效果，就不要使用多继承去实现。

12. 多使用public 少用 private
    ```
    Python 会对私有属性的名称做一些简单的变换，以保证private的字段的私密性，私有属性字段的真实姓称是： _ClassName__privateName

    def MyParentObject(object):
        def __init__(self):
            self.__private_field = 7

    def MyChildrenObject(MyParentObject):
        def get_private_field(self):
            return self.__private_filed

    baz = MyChildrenObject()
    baz.get_private_field()
    ===>> 这里是回报错的，因为在子类里面不能直接访问父类的私有实例变量，因为上面说的，私有变量实际名称是会被更改的，因此在子类里面调用父类的私有变量其实是不存在的。
    如果了解私有变量的变量名变更规则，那么在任何地方都可以调用私有变量了。

    print(baz._MyParentObject__private_field)
    ====>   7
    ```

13. camelCase 转换成 snake_case
    ```
    def camel_to_snake(name): 
    """
    A function that converts camelCase to snake_case.
    Referred from: https://stackoverflow.com/questions/1175208/elegant-python-function-to-convert-camelcase-to-snake-case
    """
    import re
    s1 = re.sub('(.)([A-Z][a-z]+)', r'\1_\2', name)
    return re.sub('([a-z0-9])([A-Z])', r'\1_\2', s1).lower()

    class SnakeCaseMetaclass(type):
        def __new__(snakecase_metaclass, future_class_name,
                    future_class_parents, future_class_attr): 
            snakecase_attrs = {}
            for name, val in future_class_attr.items(): 
                snakecase_attrs[camel_to_snake(name)] = val
            return type(future_class_name, future_class_parents,
                        snakecase_attrs)

    您可能想知道我们在这里为什么使用 __new__ 而不是 __init__。__new__ 实际上是创建实例的第一步。它负责返回类的新实例。而在另一方面，__init__ 则不会返回任何内容。它只负责在创建实例后对其进行初始化。请牢记一条简单的经验法则：当需要控制新实例的创建时使用 new，而在需要控制新实例的初始化时则使用 init。
    ```

14. 当列表不是首选时
    - 如果需要存入大量的数据时，可以使用数组（比如要存放1000万个浮点数，数组的效率会高很多，因为数组     里面并不是存放float对象，而是数字的机器翻译，就是字节表述）
    - 如果需要对列表进行先进先出的操作， deque（双端队列）的速度应该会更快。

15. 使用dis.dis反汇编函数，可以查看方法的字节码运行情况。

16. 从python3.3之后，python解释器会在创建str的时候自动计算为str分配最经济的内存，如果字符都在latin1字符集中那就使用一个字节存储每个码位，否则根据字符串中的每个字符，分配 2 或 4 个字节存储每个码位。
[详细链接](https://www.python.org/dev/peps/pep-0393/)

17. 判断对象能否被调用，最好使用内置的方法 callable()进行判断。
    ```
    [callable(obj) for obj in (len, str, 23)]

    ===>>  [True, True, False]
    ```
    
18. python里面 == 比较的是值相等，但是 is not   或是  is 比较的是对象引用空间分配的内存地址是否相等。或是可以使用 id() 这个方法判断两个变量指向的对象是否是同一个。
    ```
    >>> a = {"name": 'MrXi', "age": 27}
    >>> b = {"name": 'MrXi', "age": 27}
    
    >>> a == b
    >>> True
    
    >>> a is not b
    >>> True
    
    >>> id(a)
    >>> id(b)
    ```
    
19. 方法 ```del```不会删除对象，但是会删除对象的引用，从而导致对象无法被获取，产生对象被删除的现象。（其实对象在长时间未被使用的情况下，会被垃圾回收机制处理。也会被删除）

20. 变量保存的是引用，因此如下的规则需要谨记
    - 简单的赋值不创建副本
    - 对 += 或是 *= 所做的增量赋值来说，如果左边的变量绑定的是不可变对象，会创建新对象；如果是可变对象，会就地修改。
    - 为现有的变量赋予新值，不会修改之前绑定的变量。这叫重新绑定：现在变量绑定了其他对象。如果变量是之前那个对象的最后一个引用，对象会被当作垃圾回收。
    - 函数的参数以别名的形式传递，这意味着，函数肯能会修改通过参数传入的可变对象，这一行为无法避免，除非在本地创建副本，或者使用不可变对象。
    - 使用可变类型作为函数参数的默认值有危险，因为如果就地修改了参数，默认值也就变了，这会影响以后使用默认值的调用。