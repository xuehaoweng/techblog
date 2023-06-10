## 0.1 Python 总体架构

![图01 Python 总体架构](http://rregf9gjs.hd-bkt.clouddn.com/1041593-20220928222130764-1290175003.png)

### File Group

左边是 Python 提供的大量模块、库以及用户自定义的模块。比如 os 模块就是 Python 内置的模块。用户也可以通过自定义模块来扩展 Python 系统。

### Runtime Environment

右边是Python运行时的环境，包括对象/类型系统，内存分配器，运行时状态信息。

**运行时状态信息** 维护解释器在执行字节码时的不同状态（正常或者异常状态）之间切换的动作，我们可以把它看作一个有穷的状态机(<a target='_blank' href='https://baike.baidu.com/item/%E7%8A%B6%E6%80%81%E6%9C%BA/6548513?fr=aladdin'>状态机是什么?</a>)。

**内存分配器** 全权负责Python中创建对象，内存申请的工作。实际上他是Python运行时与C中的malloc的一层接口(<a targer='_blank' href='https://baike.baidu.com/item/malloc%E5%87%BD%E6%95%B0/8582146'>C中的malloc</a>)。

**对象类型系统** 包含Python中内存在的各种内置对象，比如整数，list和dict，以及各种用户自定义的类型和对象。


### Python Core

中间部分是Python的核心-----解释器（Interpreter），或者称为虚拟机。在解释器中，箭头的方向只是了Python运行过程中的数据流方向。

**Scanner** 对应词法分析（将文件输入的Python源码或者从命令行输入的python源码切分成一个个的token）；

**Parser** 对应语法分析（在Scanner的分析结果上进行语法分析，建立抽象的语法树AST）；

**Compiler** 根据建立的AST语法树生成指令集合（Python字节码）；

**Code Evaluator** 执行这些字节码，因此又被称为虚拟机。

## 0.2 Python源码组织

获取源码 `https://www.python.org/downloads/source/`

![Python源码目录](http://rregf9gjs.hd-bkt.clouddn.com/Python%E6%BA%90%E7%A0%81%E7%9B%AE%E5%BD%95.png)

### 源码目录解释

- Include目录
- Lib目录
- Modules目录
- Parse目录
- Objects目录
- Python目录
- PCbuild目录
- Programs目录
