ExcelAndJSON
============



*by 老G （qq 233424570）*



**Part0.缘起**
============



Excel，是游戏开发中，策划最常用的数值编辑工具。它有着公式填充，数值曲线图等许多好用功能。作为Office办公套件的一部分，它的上手度，易用性也非常不错。



JSON，是手机游戏开发中，最常用的数据交换格式。它的树形结构，让数据访问变得非常自然。并且，这种结构和脚本语言有着天然的兼容性（例如Python，JavaScript）。



基于上面的原因，在手机游戏开发过程中，很多公司都使用Excel编辑数值，然后导出JSON，最后加载到程序中。大家也都开发了许多通过Excel导出为JSON的工具。

但因为Excel是基于二维表的结构，最后导出为JSON时，JSON也是一个类似二维表的结构，这严重限制了JSON作为一种树形结构的数据交换格式的表现力和易用性。造成的结果就是，大家在开发中，不断的建表，因为二维表表现方式有限，所以只能通过当前数据获得一个主键，然后在另外的表上查找，甚至还可能出现在多个表间跳转等情况。很吃力。

除此之外，很多工具都存在着功能单一、流程复杂、配置不方便、设计不合理等问题。



为了解决上述问题，我尝试开发了这个工具。市面上大部分工具都起名为：ExcelToJSON。而我的想法是，结合Excel和JSON的优点，所以我的工具起名为：ExcelAndJSON。



**Part1.运行环境**
==============



-   支持xlsx格式的Office

-   Python 2.7

-   xlrd



**Part2.特点**
============



易用
--

-   快速的上手速度

-   配置简单，所见即所得



输出可定制
-----

-   支持各种输出格式的定制方式：数组与字典、折叠、引用、可选字段的输出



数据完整性
-----

-   单元格中的数据都会输出，不会允许跳过空数据，保证JSON结构的同一化

-   所有的属性值，如果不填写，缺省值都是null，防止bool判断出错

-   支持的字段类型较多，建议明确书写字段类型参数。如果当前单元格的数据没有填写对应的字段类型，程序会自动判断。



格式自携带
-----

-   数据与数据格式存储在同一张sheet上（主表模式除外），便于同步



**Part3.初级功能**
==============



*初级功能适用于简单的单机游戏，休闲益智类游戏，等没有太多复杂数值要处理的游戏类型。它的上手速度较快，而且也提供了足够的支持。*



**3.1 单表模式**
--------

顾名思义，单表模式就是只有一个workbook文件的模式。该模式下，我们只使用一个.xlsx文件。所有的sheet都在这个文件上。



单表模式的命令，举例如下：

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
python excel_and_json.py singlebook -o ./  -i single.xlsx
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

命令说明：

-   singlebook：开启单表模式

-   -o：输出目录

-   -i：输入的.xlsx



**3.2 表头**
------

表头决定了sheet的各种格式和内容信息
<table><tr><th>__default__</th><th></th><th></th><th></th><th></th><th></th></tr><tr><td>__folding__</td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>__type__</td><td>s</td><td>i</td><td>i</td><td>i</td><td>i</td></tr><tr><td>__name__</td><td>name</td><td>hp</td><td>mp</td><td>atk</td><td>def</td></tr></table>

表头最左侧是一些约定标记，这些标记表示了该行是什么参数类型：

-   `__type__`：必选标记，表示该行是字段类型

-   `__name__`：必选标记，表示该行是字段名

-   `__default__`：可选标记，表示该行是缺省值

-   `__folding__`：可选标记，表示该行是折叠属性，在高级功能中详述



### 字段类型

字段类型是必选标记。ExcelAndJSON支持如下的字段类型：

-   s：字符串

-   i：整数

-   f：小数

-   b：布尔

-   as：字符串数组

-   ai：整数数组

-   af：小数数组

-   d：字典，若在字典中想使用数字字符串作为值，请在数字两端加""

-   r：引用，可以引用另一张sheet中的内容，在高级功能中详述



### 缺省值

缺省值是可选标记。如果该单元格是空白，程序会自动在该单元格上填写缺省值。

-   为了保证布尔判断的正确性，以及表格结构的完整性，所有默认的缺省值都是null

-   可以自定义缺省值，但与单元格一样，需要保证数据类型的正确性



**3.3 数据区**
-------

在表头下面的，就是数据区。数据区的大小是程序自行判断的，为了保证程序正确判断数据区的单元格位置，请在数据区下面一行和最右侧一列，保持空白。在数据区之外的单元格，程序不会读取，可自由编辑。



### null

null为保留字，在单元格中可以直接填写null为占位符。程序最终会输出null到JSON结构中。



### 字段类型自动判断

如果当前单元格的数据没有填写对应的字段类型，程序将自动判断。只支持几种类型的自动判断：i，f，s。




**Part4.高级功能**
==============



*高级功能适用于比较复杂的大中型游戏，有大量数值要处理。它有一定的学习成本，但相应的提供了更强大的功能。*



**4.1 引用**
------

引用是一个字段类型，其类型值为r。引用允许在一张sheet中，插入另一张sheet中的一行。
<table><tr><th>__type__</th><th>r</th></tr><tr><td>__name__</td><td>lv1award</td></tr><tr><td>zhangsan</td><td>lvAward.lv1</td></tr></table>

在引用属性的单元格中，填写的是lvAward.lv1。该格式为约定格式“表名.记录名”。程序会在名为“lvAward”的sheet中，查找到名为“lv1”的记录，并把该记录，作为属性值插入到当前sheet中。

注意，不允许各个sheet之间循环引用。在单表模式下，被引用的表也不会输出。



**4.2 折叠**
------

折叠是一个可选字段属性。该功能允许把一张sheet中的字段，反复折叠，变成一个JSON，该JSON会填充到最后一次折叠对应的单元格中。



按JSON格式的特点，我们有两种折叠方式：

-   按{}折叠：折叠后，会生成一个字典

-   按[]折叠：折叠后，会生成一个数组



每一次折叠，不只会折叠数据。对应的字段类型，字段名，甚至折叠属性本身都会被折叠。我们约定，在折叠字段属性填写时，左侧括号后，必须紧接一个折叠后的字段名。折叠后的字段类型为d。



折叠本身的含义可能过于抽象，但其实质是非常简单的。下面演示了折叠的整个过程：



原始表
<table><tr><th>__folding__</th><th>{a{b</th><th></th><th>}</th><th></th><th>{c</th><th>}}</th></tr><tr><td>__type__</td><td>s</td><td>i</td><td>i</td><td>i</td><td>s</td><td>b</td></tr><tr><td>__name__</td><td>name</td><td>hp</td><td>atk</td><td>def</td><td>description</td><td>leader</td></tr></table>


第一次折叠
<table><tr><th>__folding__</th><th>{a</th><th></th><th>{c</th><th>}}</th></tr><tr><td>__type__</td><td>d</td><td>i</td><td>s</td><td>b</td></tr><tr><td>__name__</td><td>b</td><td>def</td><td>description</td><td>leader</td></tr></table>


第二次折叠
<table><tr><th>__folding__</th><th>{a</th><th></th><th>}</th></tr><tr><td>__type__</td><td>d</td><td>i</td><td>d</td></tr><tr><td>__name__</td><td>b</td><td>def</td><td>c</td></tr></table>


第三次折叠
<table><tr><th>__folding__</th><th></th></tr><tr><td>__type__</td><td>d</td></tr><tr><td>__name__</td><td>a</td></tr></table>


折叠后的JSON结构形如：

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    "a":{
        "b":{
            "name":"x x x",
            "hp":111,
            "atk":222
        },
        "def":333,
        "c":{
            "description":"x x x",
            "leader":false
        }
    }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


### 引用与折叠的关系

特别注意，程序在进行数据输出时。先对每个sheet进行单独处理，此时会触发折叠操作。然后如果该sheet存在引用，再插入相应的数据。所以，引用插入的数据是不可以被折叠的。




**4.3 主表模式**
--------

顾名思义，主表模式是存在一个独立的主要workbook文件，该.xlsx内部存储了一些用于输出的配置信息。一个主表workbook可以读取和加载大量的数据workbook。并按照相应的定制信息，进行sheet的输出。



主表模式的命令，举例如下：

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
python excel_and_json.py mainbook -o ./  -i main.xlsx
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

命令说明：

-   mainbook：开启主表模式

-   -o：输出目录

-   -i：输入的.xlsx



### 输入的workbook

使用`__workbook__`进行标记，在后面紧接要使用的workbook名。
<table><tr><th>__workbook__</th><th>workbook1</th><th>workbook2</th></tr></table>


### 输出的sheet

在`__workbook__`标记下面，每一行都是一个要输出sheet。每行开头为该sheet的名字。
<table><tr><th>sheet1</th><th>aaa</th><th>skill1</th><th>skill2</th><th>skill3</th><th>option</th><th>lv1</th><th>lv2</th></tr><tr><td>sheet2</td><td>name</td><td>atk</td><td>def</td><td>hp</td><td></td><td></td><td></td></tr><tr><td>sheet3</td><td>lv1</td><td>lv2</td><td>lv3</td><td></td><td></td><td></td><td></td></tr><tr><td>sheet4</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>sheet4-&gt;sheet5</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr></table>

-   选择输出的字段：如果需要字段的选择性输出，可以在sheet名后面接要输出的字段名。如果不写，则该sheet的所有字段都会被输出。

-   表改名：支持在输出时修改表的名字，方便把一个表拆成多个。表的旧名和新名之间，用->连接。



**Part5.输出BSON**
==============



*有部分同行对JSON的读取性能表示担忧。使用BSON格式可以提高数据的读取速度，但也需要配置相应的读取库。*

*实际上，在游戏开发中，JSON主要用来做一些配置信息和数值，并没有大规模的数据量，一般情况下不会成为性能瓶颈。而且因为Python不支持条件编译，增加BSON必然增加配置时间，或复杂度（通过替换文件方式）。*

可以手动添加BSON的输出支持，使用下面的库即可。

[https://github.com/martinkou/bson][1]


  [1]: https://github.com/martinkou/bson
