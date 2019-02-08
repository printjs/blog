# 更多控制流工具

## `if`语句

关键字`if`,`else`,`elif`

## `for`语句

```python
words = ["a","ab","abc"]
for w in words
    print(w,len(w))
```

若想获取数组的下标，`python`提供另外一种方式
```python
words = [1,2,3]
for i in range(len(words))
    print(i,words[i])
```

## `range` 函数

`range(start,end,step?)`

api 
    * start 生成数组的起始元素
    * end 生成数组的结束元素(不包含此元素) eg:range(0,10),包含0而不包含10`0,1,2,3,4,5,6,7,8,9`
    * step 生成数量之间的间隔 eg:range(-10, -100, -30) `-10, -40, -70`

`range(len)`

api
    * len为生成的长度，从0开始，到len-1结束

## `break`,`continue`,`else` in loops

与其他语言一致，break跳出当前的循环，continue结束当前的循环，直接开始下一次循环

## `pass`语句

pass语句什么都不做。 当语法需要语句但程序不需要操作时，可以使用它。 例如：
```python
while True:
    pass
```

## 定义函数

example
```python
    def fib(n):    # write Fibonacci series up to n
        """Print a Fibonacci series up to n."""
        a, b = 0, 1
        while a < n:
            print(a, end=' ')
            a, b = b, a+b
        print()
    fib(2000)
```

### 函数定义当中的更多
```python
def ask_ok(prompt, retries=4, reminder='Please try again!'):
    while True:
        ok = input(prompt)
        if ok in ('y', 'ye', 'yes'):
            return True
        if ok in ('n', 'no', 'nop', 'nope'):
            return False
        retries = retries - 1
        if retries < 0:
            raise ValueError('invalid user response')
        print(reminder)
```
在上述函数中，`propmt`参数是未定义的，其他两个函数都已经存在默认值了，如果不是自身需要，一般无需传递另外两个参数

```python
i = 5

def f(arg=i):
    print(arg)

i = 6
f()

# 5
```
默认值被赋予在定义的时候

> !important非常重要
> *默认值只赋值一次，这就导致当默认值为一个可变对象的时候，默认值可能不同*

```python
def f(a, L=[]):
    L.append(a)
    return L

print(f(1))
print(f(2))
print(f(3))

# [1]
# [1,2]
# [1,2,3]
```

如果你不希望在后续调用中分享值，你可以写成下面的形式
```python
def f(a,L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

### 关键值参数

`def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):`

可用的调用方式
```python
parrot(1000)                                          # 1 positional argument
parrot(voltage=1000)                                  # 1 keyword argument
parrot(voltage=1000000, action='VOOOOOM')             # 2 keyword arguments
parrot(action='VOOOOOM', voltage=1000000)             # 2 keyword arguments
parrot('a million', 'bereft of life', 'jump')         # 3 positional arguments
parrot('a thousand', state='pushing up the daisies')  # 1 positional, 1 keyword
```
不可用的调用方式
```python
parrot()                     # required argument missing
parrot(voltage=5.0, 'dead')  # non-keyword argument after a keyword argument
parrot(110, voltage=220)     # duplicate value for the same argument
parrot(actor='John Cleese')  # unknown keyword argument
```

### `*args`和`**args`

* `*args`可接受多个参数，并且参数以数组形式输出
* `**args`可接受多个参数，并且参数以字典的形式输出

example:
```python
def cheeseshop(kind, *arguments, **keywords):
    print("-- Do you have any", kind, "?")
    print("-- I'm sorry, we're all out of", kind)
    for arg in arguments:
        print(arg)
    print("-" * 40)
    for kw in keywords:
        print(kw, ":", keywords[kw])

cheeseshop("Limburger", "It's very runny, sir.",
           "It's really very, VERY runny, sir.",
           shopkeeper="Michael Palin",
           client="John Cleese",
           sketch="Cheese Shop Sketch")
```

```log
-- Do you have any Limburger ?
-- I'm sorry, we're all out of Limburger
It's very runny, sir.
It's really very, VERY runny, sir.
----------------------------------------
shopkeeper : Michael Palin
client : John Cleese
sketch : Cheese Shop Sketch
```
> 注意，*args和**args仅仅是定义时候的语法糖，调用的时候，仍然使用args来调用

### lambda表达式

一个小型的匿名函数可以通过`lambda`关键字来实现，匿名函数可以引用父级函数的作用域

### 字符串文档
example:
```python
def my_function():
    """
        Do nothing, but document it.
        No, really, it doesn't do anything.
    """
    pass
print(my_function.__doc__)
```
按照上述方式，即可打印出关于函数的文档内容

### 函数注解
example
```python
def f(ham: str, eggs: str = 'eggs') -> str:
    print("Annotations:", f.__annotations__)
    print("Arguments:", ham, eggs)
    return ham + ' and ' + eggs
f('spam')
```
```log
Annotations: {'ham': <class 'str'>, 'return': <class 'str'>, 'eggs': <class 'str'>}
Arguments: spam eggs
'spam and eggs'
```

## 编码风格

Now that you are about to write longer, more complex pieces of Python, it is a good time to talk about coding style. Most languages can be written (or more concise, formatted) in different styles; some are more readable than others. Making it easy for others to read your code is always a good idea, and adopting a nice coding style helps tremendously for that.

For Python, [PEP 8](https://www.python.org/dev/peps/pep-0008) has emerged as the style guide that most projects adhere to; it promotes a very readable and eye-pleasing coding style. Every Python developer should read it at some point; here are the most important points extracted for you:

Use 4-space indentation, and no tabs.

4 spaces are a good compromise between small indentation (allows greater nesting depth) and large indentation (easier to read). Tabs introduce confusion, and are best left out.

Wrap lines so that they don’t exceed 79 characters.

This helps users with small displays and makes it possible to have several code files side-by-side on larger displays.

Use blank lines to separate functions and classes, and larger blocks of code inside functions.

When possible, put comments on a line of their own.

Use docstrings.

Use spaces around operators and after commas, but not directly inside bracketing constructs: a = f(1, 2) + g(3, 4).

Name your classes and functions consistently; the convention is to use CamelCase for classes and lower_case_with_underscores for functions and methods. Always use self as the name for the first method argument (see [A First Look at Classes](https://docs.python.org/3/tutorial/classes.html#tut-firstclasses) for more on classes and methods).

Don’t use fancy encodings if your code is meant to be used in international environments. Python’s default, UTF-8, or even plain ASCII work best in any case.

Likewise, don’t use non-ASCII characters in identifiers if there is only the slightest chance people speaking a different language will read or maintain the code.
