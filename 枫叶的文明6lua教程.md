# 枫叶的文明6lua教程

## 前言

文明6mod制作，主要分为模型和动画、音乐、图片和图标、文本、数据库、lua程序等部分。一个mod不一定包含所有这些内容。

我们从下面的表格来理解这些部分在不同mod中的分布。

| mod类型举例           | 模型 | 动画   | 音乐 | 图片和图标 | 数据库 | lua    |
| --------------------- | ---- | ------ | ---- | ---------- | ------ | ------ |
| 新文明/领袖           | o    |        | o    | o          | o      | option |
| 新建筑/单位           | o    |        |      | o          | o      | option |
| 新奇观                | o    | option | o    | o          | o      | option |
| 皮肤mod（不影响存档） | o    |        |      |            |        |        |
| 地图mod               |      |        |      |            | o      | option |
| UI mod（不影响存档）  |      |        |      | option     |        | o      |
| UI mod（影响存档）    |      |        |      | option     | o      | o      |
| 新机制mod             |      |        |      | option     | o      | o      |

**总之，lua用来制作图形用户界面，实现modifier不能实现的高级功能，以及自定义的新机制。**可以独立使用在UI mod中，且不影响存档。也可以和数据库搭配使用（此时影响存档）。

lua程序部分是我比较擅长的内容，为了新人能够更快地入门lua，我制作了这个教程，希望可以帮到有意愿学习lua部分的文明6mod制作者。

本项目长期更新，欢迎访问项目地址以获取最新版本：

- [Gitee](https://gitee.com/FYMapleLeaves/maple_leaves-civ6)



## 致谢

我要感谢所有在lua方面对我有过帮助的modder，比如Hemmelfort、浪子、nea_baraja、皮皮凯、猭音，以及黄金时代、和而不同等mod的作者们等等。本教程也得益于这些modder的优秀代码。还要感谢菜鸟教程等互联网上编程教学资源的创建者，正是他们这种奉献交流的精神才使得更多人能够快速学会编程。



## 正文

### Chapter0: 教学资源

语言基础部分

- [lua基础，菜鸟教程](https://www.runoob.com/lua/lua-tutorial.html)
- [python基础，菜鸟教程](https://www.runoob.com/python3/python3-tutorial.html)
- [C++基础，菜鸟教程](https://www.runoob.com/cplusplus/cpp-tutorial.html)

文明6教程

- Hemmelfort的[Github](https://github.com/Hemmelfort/Civ6ModdingNotes)和[Gitee](https://gitee.com/Hemmelfort/Civ6ModdingNotes)，以及b站[视频](https://www.bilibili.com/video/BV1VW411U7tN/)和专栏。视频各方面都有涉及，方便快速入门。笔记的modinfo和lua部分很值得看。
- 兰德里奥的[文明-Mod制作教程 (gitbook.io)](https://haojun0823.gitbook.io/randerion-civ-mod-guide/)，这个主要是mod基础和数据库部分，不涉及lua，但是也很值得一看。
- 皮皮凯的[Github](https://github.com/X-PPK/pk-civ6)和[Gitee](https://gitee.com/XPPK/pk-civ6)，对数据库各个表的总结很详细，也有一些高级用法，还有对文明6官方教程的翻译。
- 优妮的新文明[音乐模板](https://github.com/dwughjsd/LandsolYuni_civ6mod)，是目前最规范的音乐做法，不涉及lua。
- Sukritact的lua接口总结[网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/)。
- 网页版[文明百科](https://www.civilopedia.net/zh-CN/gathering-storm/concepts/intro)，查阅很方便。尤其是查某个东西的变量名，就在网址最后一层。
- 文明6中文社区mod制作交流QQ群（711403691）。
- Steam格式帮助。在b站能搜到有人转载的内容。或者打开库里任意一款游戏，到商店页面，点击写评测，也能看到格式帮助。

文明6常用目录

- Log位置（伟大建设者包更新后）：**C:\Users\用户\AppData\Local\Firaxis Games\Sid Meier's Civilization VI\Logs**
- 非创意工坊mod放置位置：**C:\Users\用户\文档\My Games\Sid Meier's Civilization VI\Mods**
- 文明6游戏文件目录（Steam）：**\Steam\steamapps\common\Sid Meier's Civilization VI**
- Steam创意工坊mod文件目录：**\Steam\steamapps\workshop\content\289070**

### Chapter1: lua基础语法

学习过C或者python等编程语言的人，在接触lua之后上手会比较快。本节我们讲解lua基础，默认读者已经有一定的编程基础（如果没有的话还是去看Chapter0的lua基础吧，这一章的内容是给有其他语言基础的朋友们看的）。

#### 1.1 保留字与注释

保留字指的是一些特殊的单词，它们不能用作变量、函数名，而是用来让编译器识别lua语法。

| 布尔值，条件运算符和条件语句 | 循环语句和流程控制 | 函数     | 变量和其他 |
| ---------------------------- | ------------------ | -------- | ---------- |
| true false                   | while do           | function | local      |
| and or not                   | for in             | return   | nil        |
| if then elseif else end      | repeat until       |          |            |
|                              | break goto         |          |            |

注释使用两个减号

```lua
--
```



#### 1.2 变量和作用域，数据类型

这应该不用详细解释。变量的命名，只要不是lua的保留字就行，并且格式和其他语言也相差不多：要求以英文字母开头，可以包含数字和下划线，字母大小写不限。

变量的作用域，则具有lua本身的特点。变量默认是全局变量，除非在定义时声明是局部变量。下面展示了全局变量和局部变量的定义和赋值。

```lua
my_var_1 = 1;
local my_var_2 = 2;
```

需要注意，在文明6 lua中，我们**应该总是使用局部变量**，不管是文件中的局部变量还是函数中的局部变量。这样能保持代码的封闭性，让我们清楚地知道代码在干什么。

lua 中有 8 个基本类型分别为：nil、boolean、number、string、userdata、function、thread 和 table。

其中我们只用到userdata和thread之外的几种。看下面这个表，以简单理解这些数据类型与C++和python数据类型的大致对应关系。

| 语言   | 布尔值  | 数字              | 字符串 | 没有/空 | 数组                      |
| ------ | ------- | ----------------- | ------ | ------- | ------------------------- |
| C++    | bool    | int/float/double  | char   | void    | 顺序容器/关联容器         |
| python | bool    | int/float/complex | string | None    | List/Tuple/Set/Dictionary |
| lua    | boolean | number            | string | nil     | table                     |



#### 1.3 运算符和常用数学函数

运算符

| 关系运算符             | 逻辑运算符   | 算术运算符       |
| ---------------------- | ------------ | ---------------- |
| >   <  ==   ~=  >=  <= | and  or  not | +  -  *  /  %  ^ |

数学函数

| 数学函数           | 含义                  |
| ------------------ | --------------------- |
| `math.random()`    | [0,1)之间的随机浮点数 |
| `math.random(n)`   | [1,n]之间的随机整数   |
| `math.random(n,m)` | [n,m]之间的随机整数   |
| `math.floor()`     | 向下取整              |
| `math.ceil()`      | 向上取整              |
| `math.min()`       | 求最小值              |
| `math.max()`       | 求最大值              |
| `math.abs()`       | 求绝对值              |

一般掌握这些就足够在文明6mod中应对99%的情况。当然，也有三角函数和对数等的math函数，具体上网搜就能搜到。



#### 1.4 if语句

任何程序都离不开if语句，或者叫条件语句。在lua中，if语句有以下几个变体：

```lua
local i = 1

if i==1 then
end

if i==1 then
    local var_1 = 1
else
   	local var_2 = 2
end

if i==1 then
    local var_1 = 1
elseif i==2 then
    local var_2 = 2
elseif i==3 then
    local var_3 = 3
else
   	local var_100 = 100
end

if i==1 then
    local var_1 = 1
elseif i==2 then
    local var_2 = 2
elseif i==3 then
    local var_3 = 3
end
```

也就是说，if和end必有，elseif和else可有可无，if和elseif后面都要跟着条件和then。

条件的真假，使用关系运算符和逻辑运算符等等来设置。

#### 1.5 循环语句

##### 1.5.1 while循环

```lua
while(condition)
do
   statements
end
```

##### 1.5.2 简单for循环

for循环因为可以指定最大循环次数，不会导致死循环，因此非常推荐使用。

```lua
for var=exp1,exp2,exp3 do  
    statements  
end  
```

var从exp1变到exp2，exp3是步长，可以省略。例如

```lua
for i=1,10,1 do  
    print(i)
end  

for i=1,10 do  
    print(i)
end  
```

这两种写法是等价的。

##### 1.5.3 泛型for循环

这是用来遍历整个table（字典或数组）的循环，在文明6lua中使用的相当之多。在下面的1.6.4中会有介绍。

#### 1.6 table的使用

table是 lua 的一种数据结构，可以用来创建不同的数据类型，如：数组、字典等。

##### 1.6.1 数组和字典是什么

这里还是稍微讲一下数组和字典的概念。数组，就是我们最先接触到的一种容器，比如C语言中的数组，存一列整数

```c
int a[10];
a[0] = 1;
```

字典则是一种对应关系，比如，我们很明确，一个东西A是和另一个东西B唯一对应的，查询A的话只需要通过B去引用就可以了。这可以抽象出“**键值对**”的概念，比如python中的dict

```python
tinydict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
```

一般来说，键Key在一个字典中是唯一的，值Value则可以重复。

>小提示，键值对的概念和文明6数据库中表的主键和非主键的关系其实非常类似哦（如果你之前了解过sql的话，不了解就算了）。例如Units表中UnitType就是主键，通过它可以唯一确定某种单位在Units表中所有其他的属性，比如花费、移动力等等。实际上文明6有一套把GamePlay数据库内容映射到lua种table的机制（用来在lua里查数据库内容会让你方便），这个后面会谈到。

##### 1.6.2 以数组的方式使用table

通常，我们先建一个空table，然后使用`table.insert()`插入数据

```lua
local p_Civilizations_List = {}

table.insert(p_Civilizations_List, "CIVILIZATION_AMERICA")
table.insert(p_Civilizations_List, "CIVILIZATION_ARABIA")
table.insert(p_Civilizations_List, "CIVILIZATION_BRAZIL")
```

这样，默认是在数组的最后插入数据。引用的时候，可以直接以从1开始的整数索引来引用。

**注意，和很多其他语言不同，lua的数组型table的下标是从1开始，而不是0！（这点不知道坑了多少从其他语言过来的同学了）**

```lua
--打印CIVILIZATION_AMERICA
print(p_Civilizations_List[1])
```

但是，`table.insert()`也可以在指定位置插入，虽然在文明6mod中很少见，也确实难以想到有什么用

```lua
local p_Civilizations_List = {"CIVILIZATION_AMERICA", "CIVILIZATION_ARABIA", "CIVILIZATION_BRAZIL"}

table.insert(p_Civilizations_List, 2, "CIVILIZATION_CHINA")
```

上面都是储存字符串的数组，当然也可以用在数字类型，

```lua
local exp_max = 500
local greatPeople_Level_Required_Exp_List = {0, 15, 38, 71, 115, 169, 234, 311, 400, exp_max}
```

这里总结了一些常用的，对数组型table的操作

| 操作名                               | 含义                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| `table.insert (table, [pos,] value)` | 在table的数组部分指定位置pos插入值为value的一个元素。pos参数可选, 默认为数组部分末尾。 |
| `table.remove (table, [pos])`        | 返回table数组部分位于pos位置的元素. 其后的元素会被前移。pos参数可选, 默认为table长度, 即从最后一个元素删起。（注意，删除中间元素后，其后元素的数字索引会变化！！！这是个**易错点**！！！） |
| `#table`                             | 求table中元素个数                                            |

##### 1.6.3 以字典的方式使用table

以字典方式使用table，通常指以字符串作为键，值则可以是字符串，也可以是数字。例如

```lua
local params = {
    OnStart = 'ArtifactExtracted_GreatPeopleTraitOperation',
	UnitID = 1,
	iX = 11, 
	iY = 45,
	GreatWorkType = 14
}
```

特别地，这里的键如果是字符串，则可以不加引号！并且相应的值可以直接用.来使用和赋值：

```lua
local m_unitID = params.UnitID
```

注意，**要么以数组的方式使用table，要么以字典的方式使用table，一定不要混用**！！！

##### 1.6.4 泛型for循环

现在，有了table，我们可以使用这样的for循环来遍历table

```lua
local p_Civilizations_List = {"CIVILIZATION_AMERICA", "CIVILIZATION_ARABIA", "CIVILIZATION_BRAZIL"}
for key, value in pairs(p_Civilizations_List) do
    print("key=\t", key)
    print("value=\t", value)
end
```

当然，对于数组型的table（下标是从1开始的连续整数，且值不包括nil），也可以使用ipairs()。

**我推荐数组型的table使用ipairs()，其他情形使用pairs()。**这也是为什么table的字典和数组类型不推荐混用的原因之一（在遍历的时候容易出现奇怪现象）。



#### 1.7 字符串

lua的字符串操作，文明6mod中用到的并不很多。

其中一个比较重要的是字符串连接，使用字符串连接运算符

```lua
local my_str_1 = "sss"
local my_str_2 = "ttt"
local my_str_3 = my_str_1 ..  my_str_2 
```

然后是转义字符，一般也就用到换行\n和制表\t。

另外一些操作列在下表中，文明6mod中用不到的，就不列出了。

| 函数名                                       | 含义                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| `string.upper()`                             | 字符串全部转为大写字母。                                     |
| `string.lower()`                             | 字符串全部转为小写字母。                                     |
| `string.find (str, substr, [init, [plain]])` | 在一个指定的目标字符串 str 中搜索指定的内容 substr，<u>如果找到了一个匹配的子串，就会返回这个子串的起始索引和结束索引，不存在则返回 nil</u>。**init** 指定了搜索的起始位置，默认为 1，可以一个负数，表示从后往前数的字符个数。**plain** 表示是否使用简单模式，默认为 false，true 只做简单的查找子串的操作，false 表示使用使用正则模式匹配。 |
| `string.len()`                               | 返回所给字符串的长度，如果字符串中包含'\0'，也会被统计为一个字符。 |
| `string.sub(str, i [, j])`                   | 截取字符串。i是截取开始位置。 j是截取结束位置，默认为 -1，最后一个字符。 |
| `string.gsub(mainString,findString,replaceString,num)`    | 在字符串中替换。mainString 为要操作的字符串， findString 为被替换的字符，replaceString 要替换的字符，num 替换次数（可以忽略，则全部替换），例如<code>>string.gsub("aaaa","a","z",3);<br/>zzza    3</code>|

最后是格式化字符串（这会在文明6mod中用到吗？大概只有某些UI mod会用到）。具体的格式化字符串网上能查到。

```lua
local my_str_1 = "现在是上午%d点，温度是%4.2f度"
local my_str_2 = string.format(my_str_1, 9, 20.54)
```



#### 1.8 数据类型转换

一般只会用到字符串和数字互相转换

```lua
local my_str_1 = "666"
local my_num_1 = tonumber(my_str_1)

local my_num_2 = 777
local my_str_2 = tostring(my_num_2)
```

#### 1.9 函数

函数，和很多其他语言一样，接受参数并且有返回值。参数和返回值既可以是数字、布尔值，也可以是字符串和table

```lua
function CivilizationHasTrait(sCiv, sTrait)
    for tRow in GameInfo.CivilizationTraits() do
        if (tRow.CivilizationType == sCiv and tRow.TraitType == sTrait) then
            return true
        end
    end
    return false
end
```




### Chapter2: 简单的文明6 lua

#### 2.1 文明6的lua如何发挥作用？

文明6的lua，主要依赖于“事件”来触发程序运行。(感觉这一特点类似于Qt中的信号-槽函数机制，或者是一些UI编程中的监听器之类的)

举例说明：有一个事件 ，或者叫**Event**，是在区域建设进度更新时候触发，叫`Events.DistrictBuildProgressChanged`。我们希望在建成区域时，获得100金币。因此，我们对这个Event写一个介入的函数，如下

```lua
function On_DistrictBuildProgressChanged_ChangeGold(playerID, districtID, cityID, iX, iY,  districtType, era, civilization, percentComplete, iAppeal, isPillaged)
    --建设进度不到100，就直接返回（结束函数，事件一般没返回值的）
	if percentComplete ~= 100 then
		--print("district not completed!")
		return
	end
    --从Players字典获得玩家对象
    local pPlayer = Players[playerID]
    --送100金币
    pPlayer:GetTreasury():ChangeGoldBalance(100)

end

--将上面的函数添加到事件DistrictBuildProgressChanged里，这样以来，区域建设进度更新时候就会触发上面那个函数了（还提供了不少参数信息）
Events.DistrictBuildProgressChanged.Add(On_DistrictBuildProgressChanged_ChangeGold );
```

注意，Event是自带一些参数的，这些参数会作为我们新增的函数的参数。因此，我们在新增槽函数时，要查阅Event支持什么样的参数。

查阅游戏自带的事件，我推荐[这个网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/)，由著名modder sukritact制作，总结了文明6大部分的lua接口。



如果希望移除某个事件触发的函数，则可以使用

```
Events.XXX.Remove( func_a )
```



#### 2.2 对象的概念和文明6中的对象

##### 2.2.1 什么是对象

不知道读者是否了解编程中的“**对象**”的概念。所谓对象，和C++和python中的类，是类似的。

用通俗的话讲，人就是一种对象，可以定义各种方法/属性。比如人可以吃饭，这是一种方法；人有身高，这是一种属性。

好在文明6的lua并不需要你真的掌握面向对象，或者定义一个类（这其实在lua里比较麻烦），你只需要学会用对象就好了。


##### 2.2.2 文明6中的对象

文明6的整个游戏都充满了对象。城市是对象，区域是对象，单位是对象，玩家也是对象。

以城市为例，城市有“人口数量”这个属性，有方法来增加人口数量或者减少人口数量。以代码为例：

```lua
--获取城市对象（根据玩家ID和城市ID） 
local pCity = CityManager.GetCity(playerID, cityID)
--用城市对象获取该城市人口数量
local city_Population = pCity:GetPopulation()
--用城市对象增加该城市人口数量
pCity:ChangePopulation(1)
```
```lua
--获取单位对象（根据玩家ID和单位ID） 
local pUnit = UnitManager.GetUnit(playerID, unitID);
--用单位对象，获得该单位的坐标
local iX, iY = pUnit:GetX(), pUnit:GetY();
--用坐标获得对应的单元格对象
local pPlot = Map.GetPlot(iX, iY);
--用单元格对象 对该单元格进行了神秘操作  以后你会知道的
pPlot:SetProperty('PROP_INFRASTRUCTURE', 2);
```
**如上所述，文明6的lua，实际上就是对各种各样的对象进行操作！**

至于都有什么对象，还是推荐去看[sukritact的网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/)。

我们这里也做一简要总结：

| 对象类型    | 含义             |
| ----------- | ---------------- |
| `pCity`     | 城市             |
| `pPlayer`   | 玩家             |
| `pUnit`     | 单位             |
| `pPlot`     | 单元格           |
| `pDistrict` | 区域（使用较少） |

这些对象分别有一些子对象，例如用下面的语句获取单位经验这个子对象

```lua
--用单位对象，获得单位经验对象
local pUnitExperience = pUnit:GetExperience()
--用单位经验对象，获得这个单位的晋升列表
local promotionList :table = pExperience:GetPromotions();
--输出该单位共有几项晋升
print(#promotionList)

```

除了上述这些针对某一个具体城市或单位的对象，文明6还有一些Manager/Builder/Configuration等等名字的对象，笼统理解为管理者。管理者既有一些操作相应普通对象的方法，我们也可以通过管理者来获取普通对象，比如

```lua
--城市所有者变更 把pCity对象代表的城市，变成id为newPlayerID的玩家的城市
--ntr万岁!(不是)
CityManager.TransferCity(pCity, newPlayerID)

--获取城市 
local pCity = CityManager.GetCity(playerID, cityID)
```

>小提示：你可能注意到了，像pCity这样的具体对象通常用:调用函数，而CityManager这样的管理者对象一般用.调用函数。实际上确实如此。


##### 2.2.3 补充-modifier的主体也是一种对象

另外，补充一下modifier的对象概念。当一个modifier被连接到一个TraitType时，因为玩家只有一个，所以拥有这个modifier的对象只有一个，所以这个modifier只会生效一次。当modifier被连接到一个唯一建筑（比如奇观）时也类似。

当modifier被连接到一个可以有复数个的对象时，那么有多少个这个对象，这个modifier就生效多少次！比如单位能力，或者普通建筑/区域。这也是新手modder有时候会遇到的问题。

只要理解了“对象”的概念，您对lua和modifier的运用都会更加自如！
（当然这段看不懂也可以跳过，您总会了解到的）



#### 2.3 文明6的Gameplay环境和UI环境是什么？

很多新手会听说Gameplay环境和UI环境而不理解其含义。其实如果不做文明6mod，也遇不到这么抽象和难用的东西。

简单来说，UI主要用来读取数据，并且相应地在图形用户界面上给玩家看；除了修改官方的UI，也可以制作新的UI。Gameplay则主要用来进行改变对象属性的操作。

两者可以使用的方法（函数）和事件有共通的，也有独有的。

如何判断某种环境用什么函数？答案还是去看[sukritact的网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/)。

| 环境     | 特点                       | 如何在modinfo里使用                               |
| -------- | -------------------------- | ------------------------------------------------- |
| Gameplay | 多为操作，部分东西读取不到 | AddGameplayScripts / ImportFiles                  |
| UI       | 多为读取，一般不能进行操作 | AddUserInterfaces / ReplaceUIScript / ImportFiles |

##### 2.3.1 Gameplay环境

Gameplay环境的lua，使用AddGameplayScripts进行加载。

```xml
<AddGameplayScripts id="cpss_scripts">
	<Criteria>SocietiesModeOn_CivilizationParagons_Expansion2</Criteria>
  	<Properties>
        <LoadOrder>1000</LoadOrder>
    </Properties>
	<File>Scripts/CPSS_GreatPeopleUpgrade_Gameplay.lua</File>
	<File>Scripts/CPSS_GreatPeopleTraits_Gamplay.lua</File>
</AddGameplayScripts>
```

##### 2.3.2 UI环境

UI环境稍微复杂一些，我们这里仅仅简单介绍一下怎么加载文件，重写和新增复杂的UI实在是超出了本章的范围。

首先，可以使用AddUserInterfaces来新增UI环境的lua程序。注意，这并不意味着您一定要做一个新UI，而只是能够在UI使用某些专属函数等。

```xml
<AddUserInterfaces id="cpss_add_ui">
	<Criteria>SocietiesModeOn_CivilizationParagons_Expansion2</Criteria>
  	<Properties>
    	<Context>InGame</Context>
    	<LoadOrder>1000</LoadOrder>
  	</Properties>
	<File>UI/Additions/CPSS_GreatPeopleTraitButton.xml</File>
</AddUserInterfaces>
```

这里有两个关键点：

1. **必须在Properties里加一个Context InGame**
2. **加载.xml文件，这样会自动加载该目录下同名的.lua文件**

.xml的内容至少要包含一个Context节点，即便你没有想做新UI。下面展示了一个新的按钮UI的.xml的定义。

```xml
<?xml version="1.0" encoding="utf-8"?>

<Context>
	<Grid ID="GreatPeopleTraitGrid" Anchor="R,B" Size="auto,41" AutoSizePadding="6,0" Texture="SelectionPanel_ActionGroupSlot" SliceCorner="5,19" SliceSize="1,1" SliceTextureSize="12,41" ConsumeMouse="1" Hidden="1">
		<Button ID="GreatPeopleTraitButton" Anchor="C,B" Offset="0,-2" Size="44,53" Texture="UnitPanel_ActionButton">
			<Image ID="GreatPeopleTraitButtonIcon" Anchor="C,C" Offset="0,-2" Size="38,38"  Icon="ICON_UNITOPERATION_UPGRADE"/>
		</Button>
	</Grid>
</Context>
```

您当然也可以这样写

```xml
<?xml version="1.0" encoding="utf-8"?>

<Context>
</Context>
```



其次，如果您希望代替原来的游戏UI，那么要使用ReplaceUIScript

```xml
<ReplaceUIScript id="ingame_unitpanel_func_mode" criteria="RR_Func_Mode">
	<Properties>
        <LoadOrder>200000</LoadOrder>
    	<LuaContext>UnitPanel</LuaContext>
        <LuaReplace>UI/Replacements/UnitPanel_RealRivers.lua</LuaReplace>
	</Properties>
</ReplaceUIScript>
```

注意：

1. **LuaContext是游戏内定义好的，一般和基础游戏中的lua文件同名。不确定的话，找工坊的改UI的mod抄也行。或者参考[这个视频](https://www.bilibili.com/video/BV1q7411W7TB)，用firetuner寻找官方的Context。**
2. **LuaReplace，写你的替代lua文件的相对路径。**

具体怎么替代原来的UI，请查看第4章的详细讲解。



##### 2.3.3 哪个环境的lua会先加载？

其实，只要print试一下，就能很容易发现，在进入游戏的过程中，Gameplay环境的lua会先加载。



#### 2.4 Events和GameEvents，自定义新的GameEvents

其实是两类不同的事件，游戏都自带一些，都可以用。GameEvents只能用在Gameplay环境。



而有些mod会定义新的GameEvents，比如和而不同等等。想要这样做，需要使用下面的代码（这里的代码示例来自于浪子的奇观提醒mod）：

**1.先在Gameplay环境写以下语句，并且要确保这个lua文件的加载顺序较低。**

```lua
ExposedMembers.GameEvents = GameEvents

function ReadWonderData(retParams:table)
	retParams.wonder = Game:GetProperty(KNM_REMINDER_WONDERS)
	--print('ReadWonderData', retParams.wonder)
end

GameEvents.ReadWonderData.Add(ReadWonderData)
```

**2.再在UI环境写以下语句**

```lua
GameEvents = ExposedMembers.GameEvents;

local params = {}
GameEvents.ReadWonderData.Call(params)
```

总之，就是先在Gameplay写Add的函数，再在UI环境Call。



这样写，对于联机的影响，有人说不影响，有人说影响。

我的观点是，如果在UI端只读，肯定不影响联机。但是如果修改某些数据，有可能造成游戏不同步。



#### 2.5 一些编程规范

这里是我提倡的一些编程规范

##### 2.5.1 一种Add函数标准

很多新手，当然也包括我，最开始写触发事件的Add时，直接写在lua文件的第一层。

这样会导致一些问题，因为一些事件，例如`Events.CityAddedToMap`，会在加载存档的时候会触发，从而导致一些bug。而AddedToMap这个系列的Event都有这个通病。

所以，我们这里提出一种编程规范，所有的Add，都写在initialize函数里，并且由`Events.LoadGameViewStateDone`这个Event统一触发。它的含义是读档或者创建游戏完成。

```lua
function initialize()	
	if my_riverRoadItem ~= nil then
		Events.UnitMoved.Add( On_SetRiverRoad );
	end
	GameEvents.OnPillage.Add( OnPillage );
	Events.ImprovementAddedToMap.Add( OnImprovementAddedToMap_RemoveRoute );
	Events.ImprovementAddedToMap.Add( OnImprovementAddedToMap_SetPlotProperty );
	Events.PlayerTurnActivated.Add( On_Request_Remove_Pontoon );
end

Events.LoadGameViewStateDone.Add( initialize);
```

##### 2.5.2 一种提倡的lua文件内部结构

我建议，文件最开始，先`include`（含义见2.6节），然后是定义常量、变量，然后是基础函数、事件触发的函数，最后是初始化函数。能确定这种规范，也要感谢浪子和皮皮凯的优秀代码示范。以下是简单的示范（来自我的真实河流地图mod）：

```lua
-- ===========================================================================
-- INCLUDE
-- ===========================================================================

-- ===========================================================================
-- CONSTANTS
-- ===========================================================================
local my_riverRoadItem = GameInfo.Routes["ROUTE_RIVER_ROAD"]

-- ===========================================================================
-- VARIABLES
-- ===========================================================================


-- ===========================================================================
-- FUNCTIONS
-- ===========================================================================
--商人创建河运航线
function On_SetRiverRoad(playerID, unitID, iX, iY)
	local pUnit = UnitManager.GetUnit(playerID, unitID)
	if pUnit == nil then
		return
	end
	local unitTypeString = GameInfo.Units[pUnit:GetType()].UnitType
	if unitTypeString ~= "UNIT_TRADER" then
		return
	end

	local pPlot = Map.GetPlot(iX,iY)
	if not pPlot:IsWater() then 
		return
	end
	print("into set route")

	local routeTable = pPlot:GetRouteType()
	if routeTable ~= my_riverRoadItem.Index then
		RouteBuilder.SetRouteType(pPlot, my_riverRoadItem.Index)
	end

end
-- ===========================================================================
function initialize()	
	if my_riverRoadItem ~= nil then
		Events.UnitMoved.Add( On_SetRiverRoad );
	end
end

Events.LoadGameViewStateDone.Add( initialize);
-- ===========================================================================
print("RR function gameplay initialized!");
```



#### 2.6 什么是ImportFiles？include如何使用？

##### 2.6.1 ImportFiles用在哪里？

本节详细讲解一下ImportFiles。总的来说，会用在三类不同的文件中，请看下表：

| 文件类型 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| .dds     | 直接导入图片。优点是，不需要下载官方的美术资源和使用官方的Asset Editor；缺点是，文件较多时modinfo会变得很长。 |
| .xml     | 直接替换官方UI的同名.xml文件（或其他mod新增UI的.xml文件），也就是直接修改了控件的定义。**要慎用，能不这样做就不这样做（除非是FrontEnd的UI，必须这样做）。**CQUI就是因为修改了官方的.xml，并且删除了很多官方的控件，导致兼容性爆炸！ |
| .lua     | 直接替换官方UI的同名.lua文件（或者其他mod新增的.lua文件）。如果之前没有这个名字的文件，则把它作为一个lua模块放入游戏中（但是不直接发挥作用，需要其他文件去引用它）。 |

##### 2.6.2 include和模块的概念

接下来我们讲include的使用。首先，我们来谈谈模块的概念 。设想一下，我们有某个编程需求，比如比较两个数的大小。一般来说，对于这种比较基础的需求，编程语言都会自带一些函数来处理，比如`max()`函数。

当这种编程需求没那么基础，但是对于某些应用又是比较需要的时候，通常会把实现这种需求的函数/方法打包成一个模块，供有需要的人选择性导入，或者说import。我们能回忆起C++中的导入标准库或头文件

```c++
#include <iostream>
#include "myhead.h"
```

或者python中导入模块

```python
import numpy as np
```

需要注意的是，文明6lua和一般lua导入模块的机制并不一样！一般来说，lua的模块需要一个单独的.lua文件，以table的方式和特殊格式定义，再在其他文件中通过require()函数导入（请参考[此网站](https://www.runoob.com/lua/lua-modules-packages.html)）。但是，文明6lua采用的是include()，这是为什么呢？

```lua
--在\Base\Assets\UI\Panels文件夹下找到UnitPanel.lua文件，可以看到下面这样，导入其他lua文件的语句
include( "InstanceManager" );
include( "SupportFunctions" );
include( "UnitSupport" );
include( "Colors" );
include( "CombatInfo" );
include( "PopupDialog" );
include( "Civ6Common" );
include( "EspionageSupport" );
include("GameCapabilities");
```

这是因为，文明6的lua不是标准的lua！而是一种叫做**HavokScript**的Lua5.1的变型和扩展。我们是可以使用Agent Ransack软件在文明6游戏的.dll文件中搜到havokscript的字样的。如果有兴趣，可以看看Sukritact对这种语言的[描述文章](https://sukritact.github.io/civilization-modding-wiki/civ-6/lua/articles/About-HavokScript/)。

注意，正因为这样，所以在文明6lua中，最高只能使用Lua5.1的函数。

##### 2.6.3 include与“核心代码”

所谓“核心代码“，其实就是在要大量使用在不同lua文件中（甚至两个环境都需要 ），而又没有现成函数时，自定义的一些函数等等。

例如，我们需要这样一个函数，用来判断某个table是否包含某个value，这时候我们这样写

```lua
function TableIncludeValue(ttable, vvalue)
	for k, v in pairs(ttable) do
		if v == vvalue then 
		return true;
		end
	end
	return false;
end
```

写完了，按照简单的想法，就是在每个需要这个函数的文件里复制一份。

但是，我们也可以使用上面讲过的ImportFiles，在某个.lua文件里定义很多这样会经常用到的函数（最好不依赖于UI或Gameplay专属函数），加载顺序最小；然后在其他.lua文件里直接include，然后直接使用函数。这样就大大增强了代码的复用性！

如果您计划创作同系列的多个mod，可以先在工坊上传一个核心代码mod（例如“约会大作战：核心”或“冲击计划：萌芽”），里面包含所有常用的工具函数，然后后续这个系列的所有mod都强制依赖核心mod，并且lua的加载顺序更大。

类似地，如果您想用的某个功能函数在官方的某个.lua文件中定义过了，也可以直接include。例如，

```lua
--Summer Pockets文明mod中的SP_Gameplay.lua
include "MapUtilities"
include "SupportFunctions"
include "SP_Support"
```

其中，前两个是官方的.lua文件，最后一个其实就是Summer Pockets文明mod自定义的“核心代码”。



#### 2.7 如何获取常见对象

一般来说，我们需要获取5种常见的对象，进而可以获取出子对象。获取方式的表格如下：

玩家

| 方式       | 代码                                | 备注 |
| ---------- | ----------------------------------- | ---- |
| 通过玩家ID | `local pPlayer = Players[playerID]` |      |

城市

| 方式               | 代码                                                  | 备注 |
| ------------------ | ----------------------------------------------------- | ---- |
| 通过管理者和城市ID | `local pCity = CityManager.GetCity(playerID, cityID)` |      |
| 通过管理者和XY坐标 | `local pCity = CityManager.GetCityAt(iX, iY)`         |      |
| 通过单元格索引     | `local pCity = Cities.GetCityInPlot(iPlotIndex)`      |      |
| 通过玩家城市       | `local pCity = pPlayer:GetCities():FindID(cityID)`    |      |
| 通过某些函数       | `local pCity = pPlayer:GetCities():GetCapitalCity()`  |      |

单位

| 方式               | 代码                                                  | 备注 |
| ------------------ | ----------------------------------------------------- | ---- |
| 通过玩家ID和单位ID | `local pUnit = UnitManager.GetUnit(playerID, unitID)` |      |
| 通过玩家ID和单位ID | `local pUnit = pPlayer:GetUnits():FindID(unitID)`     |      |

单元格

| 方式           | 代码                             | 备注                                          |
| -------------- | -------------------------------- | --------------------------------------------- |
| 通过XY坐标     | `Map.GetPlot(iX, iY)`            | iX和iY都是0开始的整数，最大值和地图大小有关   |
| 通过单元格序号 | `Map.GetPlotByIndex(iPlotIndex)` | iPlotIndex是0开始的整数，最大值和地图大小有关 |

对这些对象的详细操作，请参考[sukritact的网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/)和Hemmelfort的[文明6lua笔记](https://github.com/Hemmelfort/Civ6ModdingNotes/blob/master/%E6%96%87%E6%98%8E6_Lua%E6%89%8B%E5%86%8C.md)。



#### 2.8 Events常见参数解析，以及在lua里引用数据库内容

##### 2.8.1 基础知识

我们还是以`Events.DistrictBuildProgressChanged`作为案例，这个事件的含义是某个区域的建造进度有变化

```lua
function On_DistrictBuildProgressChanged_ChangeGold(playerID, districtID, cityID, iX, iY,  districtType, era, civilization, percentComplete, iAppeal, isPillaged)

	if percentComplete ~= 100 then
		--print("district not completed!")
		return
	end
    
    local pPlayer = Players[playerID]
    pPlayer:GetTreasury():ChangeGoldBalance(100)

end

Events.DistrictBuildProgressChanged.Add( On_DistrictBuildProgressChanged_ChangeGold );
```

根据经验，我们知道playerID是玩家ID，通常是一个从0开始的整数。主流文明的玩家ID从0开始，然后是城邦。野蛮人（63）和自由城市（62）的玩家ID是固定的，这也是文明6玩家数量上限（64）的体现。

cityID则是城市ID，通过2.7节的内容，我们可以获取城市。

iX和iY是XY坐标，根据它们可以获得单元格位置或者索引。

districtType是一个从0开始的整数，它代表建造进度有变化的这个区域在数据库对应表里的Index。

**事实上，对于很多这样有对应表的东西，事件给的xxType一般都是表中的Index。**包括`pPlot:GetDistrictType()`这样的函数，返回的也是Index。

一般，对于**InGame**数据库中**单主键**的表，我们在lua里这样引用：

```lua
--通过Index引用
local districtItem = GameInfo.Districts[districtType]

--或者通过类型名（字符串）引用，程序会在主键中寻找
local districtItem = GameInfo.Districts["DISTRICT_CAMPUS"]

--获取到的districtItem代表Districts表中的一行

--可以通过districtItem获取索引，可以理解Index是表中隐藏的一列
local districtIndex = districtItem.Index
--可以通过districtItem获取区域类型名（字符串）
local districtTypeStr = districtItem.DistrictType

--可以获取其他信息，比如造价
local cost = districtItem.Cost

--特别地，如果是一个新类型区域，最好加上是否是nil的判断
local new_districtItem = GameInfo.Districts["DISTRICT_NEW_CAMPUS"]
if new_districtItem ~= nil then
    local new_cost = new_districtItem.Cost
end

--特别地，如果函数返回的区域不一定存在，也要先做判断
--如果单元格没有区域，则返回-1
local plotDistrictType = pPlot:GetDistrictType()
local plot_districtItem = GameInfo.Districts[plotDistrictType]
if plot_districtItem ~= nil then
    print("district exists")
end
```

那么，对于**双主键**的表，又怎么办呢？这个时候就需要遍历：

```lua
--比如，我们想要判断某个玩家是否有某个Trait

--首先，获取玩家配置
local playerConfig = PlayerConfigurations[playerID]
--然后，获取玩家的文明类型，这里sCiv就是文明类型名（字符串）
local sCiv = playerConfig:GetCivilizationTypeName()

--最后判断有没有Trait，可以写一个函数
function CivilizationHasTrait(sCiv, sTrait)
	for tRow in GameInfo.CivilizationTraits() do
		if (tRow.CivilizationType == sCiv and tRow.TraitType == sTrait) then
			return true;
		end
	end
	return false;
end

--sTrait就是TraitType（字符串）
--CivilizationHasTrait()函数做了一个泛型for循环来遍历双主键的表CivilizationTraits
```

##### 2.8.2 进阶做法

如果您熟悉sql语言，也许可以尝试一下`DB.Query()`这种进阶做法（可以参考皮皮凯的[教程](https://gitee.com/XPPK/pk-civ6/blob/master/%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/lua%E5%9F%BA%E7%A1%80%E5%85%B1%E9%80%9A.md#lua%E6%9F%A5%E8%AF%A2%E6%B8%B8%E6%88%8F%E6%95%B0%E6%8D%AE%E5%BA%93%E6%96%B9%E6%B3%95)）

```lua
--这样返回的是一个table，包含了很多行
--选取不是特色单位，且能训练的单位 
local results = DB.Query("SELECT * FROM Units WHERE TraitType = NULL and CanTrain = 1")

if(results ~= nil and #results > 0) then
    --做个判断，是否成功读到了数据
    print("query success")
end
```

这样做的好处是，可以直接对获取的结果进行某种限制。

`DB.Query()`只能用在**InGame**。类似地，在**FrontEnd**中可以使用`DB.ConfigurationQuery()`

```lua
--选取单人情景模式
local query = "SELECT 1 from Rulesets where IsScenario = 1 and SupportsSinglePlayer = 1 LIMIT 1";
local results = DB.ConfigurationQuery(query);
```



#### 2.9 如何在UI环境和Gameplay环境之间沟通

##### 2.9.1 使用新定义的GameEvents

这部分我们在2.4节讲过了，不再赘述。如果有疑问，可以去看和而不同mod。

##### 2.9.2 使用ExposedMembers

这种做法Hemmelfort在[视频](https://www.bilibili.com/video/BV1q7411W7TB/?spm_id_from=333.999.0.0&vd_source=72f40a88cef620ddc604e26cf3dd902f)中讲的很清楚。

首先，在功能函数的环境写这些语句

```lua
function my_func(playerID:number)
    print("666")
end

ExposedMembers.Demo = ExposedMembers.Demo or {}
ExposedMembers.Demo.my_func = my_func
```

在另一个环境就可以这样调用

```lua
ExposedMembers.Demo.my_func(my_playerID)
```

注意，这种方法中的函数参数必须是简单的数字和字符串，而不能是table。

##### 2.9.3 使用UI.RequestPlayerOperation()

这种方法适用于UI做总控，Gameplay做执行的情况。

首先，在UI写这些代码（案例来自于我的骆统界徐盛mod）

```lua
--先创建一个table，储存需要的参数
local kParameters:table = {};
kParameters.OnStart = "GetPlayerCurrentDistrictsNumberLTKWu";
kParameters.iX = unitX;
kParameters.iY = unitY;
kParameters.showText = 1;
kParameters.setProperty = -1;

--然后发送指令，要求运行名字为GetPlayerCurrentDistrictsNumberLTKWu的代码
UI.RequestPlayerOperation(iPlayer, PlayerOperations.EXECUTE_SCRIPT, kParameters)
```

在Gameplay端这样写

```lua
--获取玩家区域个数的函数
function GetPlayerCurrentDistrictsNumberLTKWu(pPlayerID:number, params:table)
	local NumberOfDistricts = 0;
	local pPlayer = Players[pPlayerID];

	if pPlayer == nil then
		print("pPlayer nil")
		return -9999
	end
	local pPlayerConfig = PlayerConfigurations[pPlayer:GetID()];
	if pPlayerConfig == nil then
		return -9999
	end
	local leader = pPlayerConfig:GetLeaderTypeName();
	if not LeaderHasTraitLTK(leader, 'TRAIT_LEADER_LUOTONG_QINZHENG') then
		return -9999
	end

	print("into calculate luotong districts")
	local pPlayerDistricts = pPlayer:GetDistricts()
	for looppPD, pPlayerDistrict in pPlayerDistricts:Members() do
		local dX, dY = pPlayerDistrict:GetLocation()	
		--print(dX, dY)
		local pPlot = Map.GetPlot(dX, dY)
		--local pdistrictID = pPlot:GetDistrictID()
		local pdistrictType = pPlot:GetDistrictType()
		--print("pdistrictID", pdistrictID)
		--print("pdistrictType", pdistrictType)
		if pPlot:GetWonderType() ~= -1 then
			--是奇观
			print("into wonder")
			if pPlot:IsWonderComplete() then
				local takeplace = 0;
			else
                --减去没建成的奇观
				NumberOfDistricts = NumberOfDistricts - 1
			end	
		end
		if pPlayerDistrict ~= nil and pPlayerDistrict:IsComplete() then
            --统计建成的区域
			NumberOfDistricts = NumberOfDistricts + 1
		end
	end

	if params.showText == 1 then
        --添加浮动文字
		Game.AddWorldViewText(0, 'LOC_QINZHENG_DISTRICT_NUMBER', params.iX, params.iY)
		Game.AddWorldViewText(0, tostring(NumberOfDistricts), params.iX, params.iY)
        --发送右边栏通知
		NotificationManager.SendNotification(pPlayerID, m_NotificationDistrictNumHash, 
		'LOC_NOTIFICATION_QINZHENG_SHOW_DISTRICT_NUM_MESSAGE', 
		Locale.Lookup('LOC_QINZHENG_SHOW_DISTRICT_NUM', Locale.Lookup(tostring(NumberOfDistricts))), params.iX, params.iY)
	else
		print("current player", pPlayerID)
		print("current player district number", NumberOfDistricts)
	end
	if params.setProperty == 1 then
		pPlayer:SetProperty(LTK_WU_QINZHENG_DISTRICT_NUMBER_KEY, NumberOfDistricts);
	end
	print("end calculate luotong districts")
	return NumberOfDistricts

end

--然后添加到GameEvents
--注意，这里的函数名可以同名也可以不同名（但是还是建议同名，避免出错）
GameEvents.GetPlayerCurrentDistrictsNumberLTKWu.Add(GetPlayerCurrentDistrictsNumberLTKWu)
```



#### 2.10 如何避免lua导致的多人联机不同步

我们只能尽量避免导致联机不同步的写法。但是相当一部分的写法的联机可用性仍然成谜。况且，就算不加任何mod，文明6联机也容易数据不同步。

目前，我们不推荐的写法：

- 使用`math.random()`随机数函数。推荐使用`Game.GetRandNum(n)`，它产生[0, n-1]之间的整数。
- 任何`ExposedMembers`
- 滥用`Game.GetLocalPlayer()`，尤其是在Gameplay环境中
- 使用部分事件，比如`Events.LocalPlayerChanged`这种带着`local`的事件



### Chapter3:  一些简单的示例

#### 3.1 特定文明建成学院送金币

我们直接上代码，请查看注释

```lua
function On_DistrictBuildProgressChanged_ChangeGold(playerID, districtID, cityID, iX, iY,  districtType, era, civilization, percentComplete, iAppeal, isPillaged)
	
    --首先判断区域是否建成
	if percentComplete ~= 100 then
		--print("district not completed!")
		return
	end
    
    --然后判断区域种类
    --通过Index引用一行
	local districtItem = GameInfo.Districts[districtType]

    --通过districtItem获取区域类型名（字符串）
    local districtTypeStr = districtItem.DistrictType
    
    if districtTypeStr ~= "DISTRICT_CAMPUS" then
        return
    end
    
    --如果要做一个文明的UA或者领袖的LA，则需要判断文明类型
    local playerConfig = PlayerConfigurations[playerID]
    --sCiv是文明类型名（字符串），判断领袖类型也类似
    local sCiv = playerConfig:GetCivilizationTypeName()
    local sLeader = playerConfig:GetLeaderTypeName()
    --或者，也可以按照2.8.1节讲过的做法来判断TraitType
    
    if sCiv ~= "CIVILIZATION_CHINA" then
        return
   	end
 	
   -- 最后加金币
    local pPlayer = Players[playerID]
    pPlayer:GetTreasury():ChangeGoldBalance(100)

end

Events.DistrictBuildProgressChanged.Add( On_DistrictBuildProgressChanged_ChangeGold );
```

这里用到了一种编程思维，那就是，先排除所有不要的情况，剩下的就是你要的情况。这样写，代码的层级就会始终保持在第一层，看着很整齐美观。不过，这个也看个人习惯，不是必须这样写。



#### 3.2 勇士攻击后获得额外经验

我们也直接上代码

```lua
function On_Combat_WarriorExtraExp( pCombatResult )
	--攻击者
	local attacker = pCombatResult[CombatResultParameters.ATTACKER]
	local attInfo = attacker[CombatResultParameters.ID]
	local attUnit = UnitManager.GetUnit(attInfo.player, attInfo.id)
	--防御者
	local defender = pCombatResult[CombatResultParameters.DEFENDER]
	local defInfo = defender[CombatResultParameters.ID]
	local defUnit = UnitManager.GetUnit(defInfo.player, defInfo.id)
    
    --以上都是固定用法，实际上pCombatResult是个有很多层的表，想知道具体结构，就要逐层print出来
    --并且，攻击者和防御者有可能一方是城市，所以实际上非常复杂
    
    --要先判断攻击者是不是单位
	if attUnit == nil then
        return
    end
    --判断攻击者是不是勇士
    if attUnit:GetType() ~=  GameInfo.Units['UNIT_WARRIOR'].Index then 
		return
	end
    --也可以仿照上节，限制玩家是xx文明或领袖，这里就省略了
    local attPlayerID = attInfo.player
    local pPlayer = Players[attPlayerID]
    
    --最后就是加经验
    attUnit:GetExperience():ChangeExperience(10);
    
end
Events.Combat.Add( On_Combat_WarriorExtraExp )
```



### Chapter4:  进阶的文明6 lua

#### 4.1 如何在存档中保存数据（Object:SetProperty()）

##### 4.1.1 为什么要在存档中保存数据

之前我们讲的所有示例，都是即时修改游戏数据，比如直接加金币、调整单位经验等等。这些操作的结果当然是会随着存档保存的。

但是，设想一下，如果想做一个复杂的效果，比如在首次建成学院时获得金币。我们很自然想到用一个变量来存储是否触发过这个增益，如果触发过则不重复触发。

如果我们仍然采取之前的做法，那么存档读档之后，这个变量原来的值就丢失了，导致每读一次档，再造个新的学院都能得到金币，这样就无法实现我们想要的效果。

这时候，我们就要用到可以把数据存到存档中的办法，那就是`Object:SetProperty()`。

##### 4.1.2 Object:GetProperty()和Object:SetProperty()

文明6中有很多对象都能使用相应的存储和读取数据的函数，这些函数的名字和参数类型也都一样。

能使用存读数据函数的对象（您可以参考[这个网站](https://sukritact.github.io/Civilization-VI-Modding-Knowledge-Base/About_Get-SetProperty)）：

| 对象          | 含义                                         |
| ------------- | -------------------------------------------- |
| Player        | 玩家                                         |
| Game          | 游戏                                         |
| Unit          | 单位                                         |
| City          | 城市                                         |
| Plot          | 单元格（请参考4.7节）                        |
| District      | 区域（需要测试，没有见过任何使用案例）       |
| PlayerManager | 玩家管理器（需要测试，没有见过任何使用案例） |

存读数据的函数

| 函数                   | 参数                                                         | 返回值 |
| ---------------------- | ------------------------------------------------------------ | ------ |
| `Object:GetProperty()` | (Key)，Key要求是字符串                                       | Value  |
| `Object:SetProperty()` | (Key, Value)，Key字符串，Value可以是number/bool/string/table |        |

需要注意的是，一般来说，UI环境只能GetProperty，而不能SetProperty。Gameplay环境则都可以。

后续，我们还会深入探讨本节内容的应用。

#### 4.2 如何修改官方的UI

本节我们介绍如何修改官方的UI。我们知道，创意工坊有很多UI优化类mod，比如什么Better开头的mod。这些mod就是在官方UI基础上修改而成。

##### 4.2.1 仅使用ImportFiles

在2.6.1节我们讲过，使用ImportFiles可以直接替换官方原文件。优点是简单，直接。但是，也有明显的缺点：

- 容易和其他用到这个.lua或.xml文件的mod冲突。
- 当其他人的mod也`ImportFiles`了这个文件，且加载顺序比你的高的时候，你的mod相当于啥都没干。

无论如何，我们还是给出示例。示例来自我的“文明6：宣传框架”mod

```lua
--详细代码就不放了
--总之，思路是，先复制原版文件内容，保持大部分不变，只修改小部分需要修改的东西
--这里就是只修改每月挑战相关的部分

--直接替换了MainMenu.lua和MainMenu.xml
--因为这是FrontEnd的UI，所以只能这样做
```

##### 4.2.2 使用ReplaceUIScript，函数重定义

这时候，我们就要讲到高级用法了。我们设想一下，一个.lua文件里有好多好多函数，而我们其实只需要改其中的一两个函数。我们可以使用老办法，管它都是干啥的，全复制过去完事。

但是，我们也可以不这样做，而只是**重定义**部分函数。

我觉得举一个官方的例子会比较好。以官方的单位面板为例，我们可以用Agent Ransack搜到，有一个`UnitPanel.lua`和一个`UnitPanel_Expansion2.lua`

```lua
--UnitPanel_Expansion2.lua
include("UnitPanel_Expansion1");
```

可以看到它先`include`了`UnitPanel_Expansion1`。这里需要非常注意，在迭起兴衰DLC中也有一个`UnitPanel_Expansion1.lua`，但是，风云变幻DLC的`UnitPanel_Expansion1.lua`和迭起兴衰的不同，而其`ImportFiles`的加载顺序更高。也就是说，在开启风云变幻DLC且游玩风云变幻规则游戏时，`UnitPanel_Expansion1.lua`的内容以风云变幻的为准。

```lua
--F:\Steam\steamapps\common\Sid Meier's Civilization VI\DLC\Expansion2\UI\Replacements\UnitPanel_Expansion1.lua
--这个文件只有一句有效语句
include("UnitPanel");
```

所以说，相当于`UnitPanel_Expansion2.lua`直接`include`了`UnitPanel.lua`。

接下来我们来讲函数**重定义**。lua是一种非常灵活的语言，可以把函数保存在变量中。文明6lua也是利用这一点来实现UI的替换。其基本形式如下：

```lua
-- ===========================================================================
--	BASE
-- ===========================================================================
--这里的my_test_fun是在官方UI的lua文件中定义过的函数
local BASE_my_test_fun = my_test_fun;
-- ===========================================================================
--	OVERRIDE
-- ===========================================================================
function my_test_fun(var)
	if condition then
        --如果满足某些条件则做相应改动
        return 666;
	else
        --否则就回到原来的情况
        return BASE_my_test_fun(var);
   	end
end
```

在`UnitPanel_Expansion2.lua`中的表现，以其中一个函数`LateCheckOperationBeforeAdd`为例，就是

```lua
-- ===========================================================================
--	BASE
-- ===========================================================================
local BASE_LateCheckOperationBeforeAdd = LateCheckOperationBeforeAdd;
-- ===========================================================================
-- Override the unit operation icon for XP2 railroads.
-- ===========================================================================
function LateCheckOperationBeforeAdd( tResults: table, kActionsTable: table, actionHash:number, isDisabled:boolean, tooltipString:string, overrideIcon:string )
	if (tResults[UnitOperationResults.ROUTE_TYPE] ~= nil and tResults[UnitOperationResults.ROUTE_TYPE] == "ROUTE_RAILROAD") then
		overrideIcon = "ICON_ROUTE_RAILROAD";
		return isDisabled, tooltipString, overrideIcon;
	end

	-- Not a railroad, fall through to the base version.
	return BASE_LateCheckOperationBeforeAdd( tResults, kActionsTable, actionHash, isDisabled, tooltipString, overrideIcon );
end
```

这个重定义函数的功能就是，如果单位要建造的是铁路，就把动作图标换成铁路图标，否则就不变。最后放一下风云变幻的modinfo部分。

```xml
	<ReplaceUIScript id="Expansion2_UnitPanel" criteria="Expansion2">
			<Properties>
				<LuaContext>UnitPanel</LuaContext>
				<LuaReplace>UI/Replacements/UnitPanel_Expansion2.lua</LuaReplace>
        </Properties>
	</ReplaceUIScript>
```

以上就是替换官方UI的方法。总结一下：

- 先`include`原来的.lua文件
- 以重定义的方式修改要修改的某些函数
- 最后在`InGame`中`ReplaceUIScript`（请参看2.3.2节）



##### 4.2.3 如何在替换官方UI的同时，保持兼容

如果我们希望，mod1被加载顺序靠后的mod2兼容，那么对mod1来说：

- 首先，需要像4.2.2节讲的一样，以`include`原文件+重定义的方式来修改某些函数。文件的命名方式可以是原文件名+下划线+后缀。
- 其次，在`ReplaceUIScript`的同时，还需要同时`ImportFiles`（且这两项的加载顺序一样）。

然后，对于mod2来说，需要：

- 以`include`原文件+重定义的方式来修改某些函数，**且要`include`mod1中相同LuaContext的文件。**文件的命名方式可以是原文件名+下划线+后缀。
- 在mod1的加载顺序后加载`ReplaceUIScript`。如果希望mod2之后被mod3兼容，则最好也同时`ImportFiles`（且这两项的加载顺序一样）。

当然，最好也举个例子。以我的真实河流地图mod为例，其中的UnitPanel_RealRivers.lua如下

```lua
--首先include官方的单位面板lua
include("UnitPanel.lua")
pcall(function() include("UnitPanel_Expansion1.lua"); end);
pcall(function() include("UnitPanel_Expansion2.lua"); end);

--然后兼容工坊主流的修改单位面板的mod
print("========================Real Rivers Loading Files========================")
--pcall相当于python中的try except，不过在这里其实没什么用，因为pcall函数里的include不报错（即便引用了不存在的lua文件）
--甚至不用pcall，直接include不存在的lua文件也不报错

--按照LoadOrder顺序

--0
--Sun and Moon of Teyvat Pack 提瓦特的日月包
local s_m_in_use = pcall(function() include("UnitPanel_Klee.lua"); end);
--print("Sun and Moon in use=", s_m_in_use)

--0
--约会大作战
local dal_in_use, r_value = pcall(
		function() 
			local my_value = 1
			include("DAL_UnitPanel.lua"); 
			my_value = 2
			return my_value
		end
);
--print("DAL in use=", dal_in_use)
--print("DAL r_value=", r_value)

--0
--建造者和军事工程师卡顿修复 Builder / Military Engineer Lag Fix
local lagFix_in_use = pcall(function() include("LagFixHotkey.lua"); end);

--11
--AB2531 Plague Mod 黑死病情景模式mod
local plague_in_use = pcall(function() include("UnitPanel_BlackDeathScenario.lua"); end);
--print("Plague in use=", plague_in_use)

--1000
--Better Unit List
--no replace ui script，无法兼容

--9060
--黄金时代
local ga_in_use = pcall(function() include("GoldenAge_UnitPanel.lua"); end);
--print("GA in use=", ga_in_use)

--10000
--Fuzzle's Secret Societies: Brigand Court
--no import files，无法兼容

--150000
--和而不同
local hd_in_use = GlobalParameters.MOD_HD_IN_USE_RR or 0;
if hd_in_use > 0  then
	include("DL_UnitPanel.lua")
	print("HD in use, Real Rivers loading DL_UnitPanel.lua")
end
print("========================End Real Rivers Loading Files========================")
```



#### 4.3 如何新增一个UI

这方面，其实Hemmelfort的视频讲的很好，除了新增UI之外还讲了使用firetuner寻找`Context`。请参考[这个视频](https://www.bilibili.com/video/BV1q7411W7TB)。

##### 4.3.1 使用ChangeParent添加单位面板新按钮

这里我们以提瓦特的日月包mod为例（代码来自浪子）。首先，需要定义UI的部件。这里就定义了一个`Grid`（布局）和一个带有`Image`的`Button`（按钮）。设置默认隐藏，也就是`Hidden="1"`

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--Written by Konomi-->
<Context>
	<Grid ID="SumeruRangerGrid" Anchor="R,B" Size="auto,41" AutoSizePadding="6,0" Texture="SelectionPanel_ActionGroupSlot" SliceCorner="5,19" SliceSize="1,1" SliceTextureSize="12,41" ConsumeMouse="1" Hidden="1">
		<Button ID="SumeruRangerButton" Anchor="C,B" Size="44,53" Texture="UnitPanel_ActionButton">
			<Image ID="SumeruRangerButtonIcon"		Anchor="C,C" Offset="0,-2" Size="38,38"  Icon="ICON_UNITOPERATION_PLANT_FOREST"/>
		</Button>
	</Grid>
</Context>
```

然后在lua中写逻辑。因为是单位面板的新按钮，要绑定到单位面板，并且在某些时候刷新。我们把所有细节删除掉，得到下面这些骨干（细节请看原文件）

```lua
-- Written by Konomi, edited by UzukiShimamura

-- ===========================================================================
--	CONSTANTS
-- ===========================================================================
--一些和游戏速度相关的常量
local GAME_SPEED = GameConfiguration.GetGameSpeedType()
local GAME_SPEED_MULTIPLIER = GameInfo.GameSpeeds[GAME_SPEED] and GameInfo.GameSpeeds[GAME_SPEED].CostMultiplier / 100 or 1
local COOLDOWN_TURN = math.floor(GlobalParameters.SUMERU_RANGER_COOLDOWN_TURN * GAME_SPEED_MULTIPLIER) or 50
-- ===========================================================================
--	VARIABLES
-- ===========================================================================
local m_BaseYields = {
	FEATURE_FOREST = {YIELD_PRODUCTION = 0},
	FEATURE_JUNGLE = {YIELD_PRODUCTION = 0},
}
local m_ForestInfo = GameInfo.Features['FEATURE_FOREST']
local m_JungleInfo = GameInfo.Features['FEATURE_JUNGLE']

-- ===========================================================================
function IsButtonHide(pUnit)
	--返回一个布尔值，用于判断要不要隐藏按钮
	return false
end
-- ===========================================================================
function IsButtonDisabled(pUnit)
	--返回一个布尔值和一个本地化文本，用于判断要不要禁用按钮，以及禁用原因
	return true, Locale.Lookup('LOC_SUMERU_RANGER_DISABLED_FEATURE_TOOLTIP')
end
-- ===========================================================================
function Refresh()
    --刷新按钮状态和tooltip
	local pUnit = UI.GetHeadSelectedUnit()
	if pUnit == nil then
		return
	end

	if IsButtonHide(pUnit) then
		Controls.SumeruRangerGrid:SetHide(true)
	else
		Controls.SumeruRangerGrid:SetHide(false)
		
		local disabled, reason, featureIndex = IsButtonDisabled(pUnit)
		Controls.SumeruRangerButton:SetDisabled(disabled)

		local tooltip = Locale.Lookup('LOC_SUMERU_RANGER_OPERATION_TOOLTIP', COOLDOWN_TURN)
		
		if not disabled then
			local _, yieldTooltip = Calculate(featureIndex)
			Controls.SumeruRangerButton:SetToolTipString(tooltip .. '[NEWLINE]' .. Locale.Lookup('LOC_SUMERU_RANGER_OPERATION_YIELD_TOOLTIP', yieldTooltip))
		else
			Controls.SumeruRangerButton:SetToolTipString(tooltip .. '[COLOR:Red]' .. reason .. '[ENDCOLOR]')
		end
	end  
end
-- ===========================================================================
function OnUnitMoveComplete(playerID, unitID, iX, iY)
	if playerID ~= Game.GetLocalPlayer() then
		return
	end
	Refresh()
end
-- ===========================================================================
function OnUnitSelectionChanged(playerID, unitID, plotX, plotY, plotZ, bSelected, bEditable)
	if playerID ~= Game.GetLocalPlayer() then
		return
	end
    if bSelected then
        Refresh()
    end
end
-- ===========================================================================
function OnButtonClicked_PatrolForest()
    --按下按钮就执行这个函数
	local pUnit = UI.GetHeadSelectedUnit()
	local iX = pUnit:GetX()
	local iY = pUnit:GetY()
	local unitID = pUnit:GetID()
	local iPlayer = Game.GetLocalPlayer()
	local pPlot = Map.GetPlot(iX, iY)
	local featureType = pPlot:GetFeatureType()
	
    --播放动画
	SimUnitSystem.SetAnimationState(pUnit, "ACTION_1", "IDLE")
	--执行巡林，用法我们在2.9.3节讲过
	UI.RequestPlayerOperation(iPlayer, PlayerOperations.EXECUTE_SCRIPT, {
		OnStart = 'SumeruRangerOperation',
		X = iX,
		Y = iY,
		UnitID = unitID,
		Yields = Calculate(featureType)
	})

	Controls.SumeruRangerGrid:SetHide(true)
end
-- ===========================================================================
function Initialize()
    --获取单位面板的路径
	local pContext = ContextPtr:LookUpControl("/InGame/UnitPanel/StandardActionsStack")
	if pContext ~= nil then
        --把自定义按钮绑定到单位面板
		Controls.SumeruRangerGrid:ChangeParent(pContext)
        --把行为函数绑定到按钮（也就是，按下按钮就执行这个函数）
		Controls.SumeruRangerButton:RegisterCallback(Mouse.eLClick, OnButtonClicked_PatrolForest)
	end
	--做一些初始化
	InitializeData()
end
--初始化
Events.LoadGameViewStateDone.Add(Initialize)
--在单位移动或单位选择后，判断要不要刷新按钮
Events.UnitSelectionChanged.Add(OnUnitSelectionChanged)
Events.UnitMoveComplete.Add(OnUnitMoveComplete)

-- ===========================================================================
```



##### 4.3.2 LuaEvents的使用

本节和下一节，我们讲解，如何添加一个从左上菜单栏（伟人、巨作、天气、总督等等，那一排水平按钮的位置）打开的新UI。

首先，我们要知道，按钮和由它打开的面板实际上是两个`Context`的内容，也就是说，我们需要先讲解如何在两个UI之间通信。这是通过LuaEvents来完成的。我们还是来举个官方的例子，比如巴比伦包的英雄相关

我们在`\Sid Meier's Civilization VI\DLC\Babylon\UI\Additions`下找到`HeroesPopup.lua`，这个是发现英雄时候的弹窗对应的lua。其中有这样的代码

```lua
Controls.LookAtHeroButton:RegisterCallback(Mouse.eLClick, function() 
    ShowHeroInGreatPeoplePopup(kHeroDef);
end);
```

这句话注册和绑定了一个函数，使得我们点击`LookAtHeroButton`后，就会跳转到英雄详情面板（伟人面板的一页）。这里的`ShowHeroInGreatPeoplePopup`代码如下

```lua
function ShowHeroInGreatPeoplePopup( kHeroDef:table )
    --触发一个LuaEvents
	LuaEvents.HeroesPopup_ShowNewHero(kHeroDef);
    --关闭发现英雄弹窗
	Close();
end
```

那么我们要问了，`LuaEvents.HeroesPopup_ShowNewHero`是哪里定义的呢？**其实这句代码既是定义又是触发。**

然后我们转到伟人面板的lua，也就是同目录下的`GreatPeopleHeroPanel.lua`，可以看到

```lua
function OnHeroesPopup_ShowNewHero( kHeroDef:table )
	m_newestHeroType = kHeroDef.HeroClassType;
    --再触发显示整个伟人面板的LuaEvents，这句代码也既是定义又是触发，它连接到巴比伦包中replace伟人面板的lua中
	LuaEvents.GreatPeopleHeroPanel_Show();
end

LuaEvents.HeroesPopup_ShowNewHero.Add(OnHeroesPopup_ShowNewHero);
```

好了，我们既然已经知道了UI之间的通信方式，那么就可以实现“**点击一个按钮，打开一个面板**”了。

##### 4.3.3 添加通过左上栏打开的新UI

先咕咕咕了，感兴趣可以去看浪子的详细奇观提醒，或者Ophidy的上古之遗模式，或者我的音乐播放器mod。



#### 4.4 什么是Hash

哈希函数（Hash Function）是一种可以将任意长度的消息M映射成为一个长度较短且长度固定的值H（M）的算法，称H（M）为哈希值、散列值（Hash Value）。它是一种单向密码体制，即一个从明文到密文的不可逆映射，只有加密过程，没有解密过程。虽然理论上存在两个不同的输入能够对应同一个输出，但是寻找是很困难的一件事情，可能要花费很长时间。

文明6中的Hash值，通常出现在某类对象中，或者在**Types**表中定义过的一些东西（详细内涵我们不用深究，只需要知道怎么用），比如，类似**District/Building/GreatPersonIndividual**等等。

通常，这些有**Hash**的东西具有以下特点：

- 在**Types**表中定义过（当然，有些东西似乎不符合这一项，或者是我没仔细看）

- 在数据库中通常是单主键的表

- 可以通过某一行来引用其Hash值

  解释如下

  ```lua
  --引用某种资源的Hash
  local pResourceHash = GameInfo.Resources["RESOURCE_IRON"].Hash
  ```

- 可以通过`DB.MakeHash()`或其他函数来得到其Hash值

  解释如下：

  ```lua
  --引用某种通知的Hash
  m_SecretSocietyLevelUpHash = DB.MakeHash("NOTIFICATION_SECRETSOCIETY_LEVEL_UP");
  
  --也有个这个函数，没细看是干什么的
  BANNERTYPE_QHAPAQ_NAN = UIManager:GetHash("BANNERTYPE_QHAPAQ_NAN");
  
  --滤镜也有自己的特殊函数
  local m_MovementZoneOfControl : number = UILens.CreateLensLayerHash("Movement_Zone_Of_Control");
  
  --这个也没细看，也和UI有关
  local buildingHash :number = UI.GetInterfaceModeParameter(CityOperationTypes.PARAM_BUILDING_TYPE);
  ```

- 有某些函数参数要求用Hash值，或者函数返回值是Hash

  举例如下：

  ```lua
  --函数参数要求Hash
  local canStart:boolean, results:table = UnitManager.CanStartOperation( m_spy, operation.Hash, cityPlot, false, true);
  
  --函数返回Hash
  local eUnitType = pUnit:GetTypeHash();
  local currentProductionHash :number = pBuildQueue:GetCurrentProductionTypeHash();
  local districtHash:number	= UI.GetInterfaceModeParameter(CityOperationTypes.PARAM_DISTRICT_TYPE);
  ```

- 有时候Hash可以代替Index来引用数据库

  解释如下：

  ```lua
  --引用项目
  local projectDef	:table = GameInfo.Projects[hash];
  ```

- Hash值常常用来比较是否相等，类似于比较Type变量名

  解释如下：

  ```lua
  local NATIONAL_PARK_LAYER_HASH  = UILens.CreateLensLayerHash("MapLabels_NationalParks");
  if layerHash == NATIONAL_PARK_LAYER_HASH then
      --如果滤镜是国家公园滤镜
  end
  
  if (actionHash == UnitOperationTypes.FOUND_CITY) then
      --如果单位行动是建立城市
  end
  ```

  

#### 4.5 虚拟建筑

虚拟建筑，指的是一些用作功能性的建筑，通常不是文明的特色建筑，也没有模型，图标也是随便设置或不设置。通常用在以下几种情况：

- 作为某些建筑或项目的前置

  解释：某些市中心建筑，假设我们想要在城市建成一个专业化区域后才能建设此建筑，那么我们就需要一种叫做“城市至少拥有一个专业化区域”的虚拟建筑，使用lua检测到已经建成一个专业化区域后，从lua添加此虚拟建筑，从而达到我们的目的。（请参考黑暗时代mod）

- 切换UA或LA

  解释：我们知道文明的UA或LA上面挂的`Modifier`一般是一直生效的，所以当我们想做一个拥有几种不同LA的领袖，并且可以在这些LA之间切换的话，就可以使用虚拟建筑上挂`BuildingModifier`的形式。其切换可以通过类似发电厂转换项目的形式，也可以纯粹用lua控制

- 作为新系统的能力实现

  解释：可以查看Ophidy的上古之遗模式，遗珍的主动能力就是通过虚拟建筑实现的。

现在，虚拟建筑已经比较成熟，通常其需要这样定义（感谢姥鲨的指导！）：

- `PrereqDistrict`不填。建筑是可以不要区域的，只要不需要区域，就不会被罗马、奇观、瓦莱塔的能力影响到。虽然其默认位置还是在市中心。
- `Mustpurchase=1`但不写`PurchaseYield`，这样生产界面看不到（无法生产），购买界面也看不到（无法购买）。并且填`CivilopediaPageExcludes`表，从而不显示在百科中。

虽然如此，虚拟建筑有时候也会有奇怪的bug，比如受到建成建筑送百分比产出的`Modifier`影响，以及占领城市后建筑无法修复（需要用lua拆了重新给），拆了重新给的时候一些`Modifier`会重复生效（比如送单位的`Modifier`）。

虚拟建筑的赠送和移除：

```lua
--赠送
pCity:GetBuildQueue():CreateBuilding(GameInfo.Buildings['BUILDING_XXX'].Index)
--移除
pCity:GetBuildings():RemoveBuilding(GameInfo.Buildings['BUILDING_XXX'].Index)
```



#### 4.6 虚拟改良

虚拟改良，至少有以下3种用处：

- 替换成其他东西。

  也就是，把区域、资源、特殊单位行为等等以改良的形式呈现，然后在这个改良被造好的时候利用`Events.ImprovementAddedToMap`来把这个虚拟改良瞬间替换成区域等。或者，也可以替换成资源，从而实现建造者种资源。优点是可以利用官方的UI，不用写新的UI。

- 和区域或建筑配合使用，用来实现传送单位（不是百慕大那种随机的）。

  因为目前只能靠给改良加`property`的方式实现传送，所以如果想把能力写给建筑，就要在建筑建成时（`GameEvents.BuildingConstructed`），在建筑所在的单元格上加一个虚拟改良。这里用到了一个隐藏特性，也就是一个单元格上可以同时有区域和改良！

  详细做法，请参照浪子的雷电将军mod。

- 利用其没有模型的特性，来实现禁止某些单元格上人口（因为改良有不可工作属性）。

  请参考C1sen的“更真实的保护区“mod。



#### 4.7 使用PlotProperty

我们在前面4.1节提到过，lua中，可以对某个单元格设置`Property`。单单设置它，毫无用处，关键还是在配合一个神奇的`Requirement`，也就是`REQUIREMENT_PLOT_PROPERTY_MATCHES`，它检测的实际上是城市市中心单元格的`Property`。

这种方法的集大成者就是和而不同的产出辐射系统。为了看懂这部分代码，您需要同时具有很高的sql水平。其要点就是，`Modifier`是本来就连接到城市的，但是使用lua来设置`PlotProperty`，搭配`REQUIREMENT_PLOT_PROPERTY_MATCHES`来控制其激活与否。这样就实现了使用lua来灵活控制`Modifier`的开关。



#### 4.8 使用AttachModifierByID

`AttachModifierByID()`是一种在lua中动态（后天）赋予能力的做法。但是要注意，`Attach`之后的`Modifier`会永久存在在对象身上。目前，只能对`pPlayer`和`pCity`使用`AttachModifierByID()`。

首先，您需要在数据库中先定义好`Modifier`，并且一般要求是适用于`pPlayer`和`pCity`的`Modifier`。

为了理解这一点，请看表格：

| 用法                                     | 备注                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| `pPlayer:AttachModifierByID(ModifierId)` | 适用于玩家的`Modifier`，通常可以套用或者仿写`Trait/Policy`这样对玩家来说唯一的对象上的`Modifier`。范围相对比较广，因为往往可以从玩家出发得到首都、玩家所有城市、玩家所有区域等等对象。 |
| `pCity:AttachModifierByID(ModifierId)`   | 适用于单个城市的`Modifier`，通常可以套用或仿写长得像`_SINGLE_CITY_`这样的`Modifier`。相对没那么自由，但是好处是直接给特定城市，条件限制可以在lua中完成。举例：单城产出。 |



#### 4.9 一种在UI环境存档保存数据的方法

某天在看工坊mod代码的时候，偶尔发现了一种上古时期在UI环境保存数据的办法（读档不消失）。

其基本内涵是利用`PlayerConfigurations`来存储数据。这可能是从官方的`SetupParameters.lua`中逆向出来的。

```lua
--读取
local dataDump_info = PlayerConfigurations[playerID]:GetValue( m_Key )  
--存储
PlayerConfigurations[playerID]:SetValue(m_Key, m_Value);
```

其中`Key`是一个字符串，类似`Property`的做法。但是，问题在于可以存的东西并不是随心所欲的，所以上古时期的大佬使用了非常神奇的方法，把要存的表转化成一个字符串（？此处我没完全看懂，所以可能有误），再存入。这类似于序列化。

我们简写如下，可以照猫画虎：

```lua
local ML_Music_Player_Info_Key	= "MAPLE_LEAVES_MUSIC_PLAYER_INFO_KEY"

--读取
function On_LoadMusicPlayerSetting()

	local player_MusicPlayLists_Info = {}

	local dataDump_info_0 = PlayerConfigurations[0]:GetValue( ML_Music_Player_Info_Key )  
	if dataDump_info_0 ~= nil then
		loadstring(dataDump_info_0)();
		player_MusicPlayLists_Info = temp_player_MusicPlayLists_Info
	end

	return player_MusicPlayLists_Info
end

--存储
function On_SaveMusicPlayerSetting(player_MusicPlayLists_Info)

	local dataDump_info = DataDumper(player_MusicPlayLists_Info, "temp_player_MusicPlayLists_Info");
	--print("dataDump_info:");
    --print(dataDump_info);

	PlayerConfigurations[0]:SetValue(ML_Music_Player_Info_Key, dataDump_info);

end
```

然后，需要引用`DataDumper.lua`。这个在很多利用这个方法的mod中都可以找到，可以看BetterTradeScreen或者我的音乐播放器mod。



#### 4.10 如何节省lua的性能

当有很多新文明mod都使用lua来写特殊能力时，一个Event可能会触发很多个槽函数，这样会导致性能问题，尤其是在过回合的时候（过回合时会触发很多Event，进而触发很多槽函数）。我们现在提供一种可能的节省性能的方式，那就是选择性Add槽函数！

其内涵很容易理解。假设我们有一个领袖`LEADER_A`，他有一个特质`TRAIT_A_XX`，然后我们就按照通常的方式，在lua端检测这个TraitType来实现功能

```lua
function On_SampleEvent_Action(playerID)  
    local playerConfig = PlayerConfigurations[playerID];
    local pCiv = playerConfig:GetCivilizationTypeName();
    --如果没有TRAIT_A_XX，就返回
    --这个函数我们之前定义过，请回顾1.9节
    if not CivilizationHasTrait(pCiv, 'TRAIT_A_XX') then
        return
    end
    
    local pPlayer = Players[playerID]
   	--在这里做操作
end

function initialize()	
    --这个事件的名字是我瞎编的
	Events.SampleEvent.Add( On_SampleEvent_Action );
end
Events.LoadGameViewStateDone.Add( initialize);
```

这些代码在有`LEADER_A`出场的时候当然没什么问题，但是如果这一局游戏没有他出场，这时候lua函数就开始浪费性能了。因此，有必要在刚开始的时候就检测这局游戏有没有他出场，如果出场了，再`Add`槽函数。示例如下：

```lua
function On_SampleEvent_Action(playerID)  
    local playerConfig = PlayerConfigurations[playerID];
    local pCiv = playerConfig:GetCivilizationTypeName();

    if not CivilizationHasTrait(pCiv, 'TRAIT_A_XX') then
        return
    end
    
    local pPlayer = Players[playerID]
   	--在这里做操作
end

function initialize()	
    --检测这局游戏有没有具有TRAIT_A_XX这个Trait的玩家
    --遍历所有主流文明玩家
    for _,playerID in ipairs(PlayerManager.GetWasEverAliveMajorIDs()) do
        local playerConfig = PlayerConfigurations[playerID];
        local pCiv = playerConfig:GetCivilizationTypeName();
        if CivilizationHasTrait(pCiv, 'TRAIT_A_XX') then
            Events.SampleEvent.Add( On_SampleEvent_Action );
            break
        end
    end
end
Events.LoadGameViewStateDone.Add( initialize);
```

或者，您也可以先全都`Add`，然后遍历玩家后再相应`Remove`掉。甚至可以在有玩家被打败后重新检测一遍，半路上`Remove`掉。

### Chapter5:  实用小技巧

#### 5.1 通过lua送伟人

这里的示例来自于我的文明典范mod

```lua
local iEra = Game.GetEras():GetCurrentEra();

--这里pIndividualType是个从table中抽出来的字符串
local pIndividualType = tList[iRandom]
local pIndividualHash = GameInfo.GreatPersonIndividuals[pIndividualType].Hash

Game.GetGreatPeople():GrantPerson(pIndividualHash, "GREAT_PERSON_CLASS_PROPHET_CPSS", iEra, 0, ePlayer, false);

--有必要讲一下这个函数的参数
--首先，第一个是伟人个体的Hash，直接通过数据库的行.Hash就能获得
--第二个参数是伟人种类，用字符串，Hash和Index都可以，这一点是大大出乎我的预料了（HD的客卿用的是Index，努比亚和亚历山大情景用的是Hash，我这里用的是字符串）
--第三个参数是时代，直接照抄
--第四个参数是cost，填0就行，填其他数没有测试过会发生什么
--第五个参数是玩家ID，整数
--最后一个参数未知含义，所以照抄官方的写法，填false
```

注意，如果是在伟人面板显示的伟人种类，则有可能被其他玩家先招募而无法成功送。所以，最好写成专属伟人种类。



#### 5.2 添加右边栏通知，lua引用本地化文本

首先，需要写数据库部分：

```xml
<GameData>
	<Types>
    	<Row Type="NOTIFICATION_MY_TEST" Kind="KIND_NOTIFICATION"/>
	</Types>
	
    <!--summary和message都填好，方便lua直接用-->
	<Notifications>
		<Row NotificationType="NOTIFICATION_MY_TEST" SeverityType="MID" ExpiresEndOfTurn="True" 											AutoNotify="False" AutoActivate="False" 
            Message="LOC_NOTIFICATION_MY_TEST_MESSAGE" 
            Summary="LOC_NOTIFICATION_MY_TEST_SUMMARY"/>
	</Notifications>
</GameData>
```

再写lua部分（UI和Gameplay均可）：

```lua
--首先获取数据库的某行
local m_NotificationInfo = GameInfo.Notifications['NOTIFICATION_MY_TEST']
--然后新建一个table存储信息
local notificationData = {}
--这里方括号内是固定用法
notificationData[ParameterTypes.MESSAGE] = Locale.Lookup(m_NotificationInfo.Message)
notificationData[ParameterTypes.SUMMARY] = Locale.Lookup(m_NotificationInfo.Summary)
notificationData[ParameterTypes.LOCATION] = { x = params.X, y = params.Y }
--第一个参数是playerID，第二个是通知的Hash，最后一个是信息table
NotificationManager.SendNotification(playerID, m_RewardNotificationInfo.Hash, notificationData)	
```

注意，如果您想要送达的通知中含有某个变量，您需要先在文本中写：

```xml
<GameData>
    <LocalizedText>
		<Replace Tag="LOC_NOTIFICATION_MY_TEST_MESSAGE" Language="zh_Hans_CN">
			<Text>简略信息</Text>
		</Replace>
        <!--有2个信息，就写2个{}-->
		<Replace Tag="LOC_NOTIFICATION_MY_TEST_SUMMARY" Language="zh_Hans_CN">
			<Text>详细信息，玩家id是{1_Playerid}，通知数值是{2_Num}。</Text>
		</Replace>
	</LocalizedText>
</GameData>
```

然后在lua中写：

```lua
notificationData[ParameterTypes.SUMMARY] = Locale.Lookup(m_NotificationInfo.Summary, playerID, 666)
```

这也是lua中文本实现可变字符串的方法，使用`Locale.Lookup()`函数，第一个参数是已经在文本中定义好的“LOC_”变量，后面可变个数的变量则是要替换`{}`部分的值。注意，`{}`部分的变量，要求是从1开始，加下划线的字符串。

另外，您也可以不采用`table`，而是直接写：

```lua
local my_Notification_Hash = DB.MakeHash("NOTIFICATION_MY_TEST")
NotificationManager.SendNotification(playerID, my_Notification_Hash, 
    						Locale.Lookup('LOC_NOTIFICATION_MY_TEST_MESSAGE'), 
							Locale.Lookup('LOC_NOTIFICATION_MY_TEST_SUMMARY', playerID, 666), iX, iY)
```



#### 5.3 实现单位可变加力

实现单位可变加力，使用的是与结社模式吸血鬼类似的写法。顺带吐槽一下，结社模式的数据库全都写在一个文件里，结构很不清晰，尤其是吸血鬼和吸血鬼城堡相关的部分很难看懂。

总之，我们抽丝剥茧，可以知道吸血鬼能够可变加力，是因为使用了两个特殊的`ModifierType`

| ModifierType                           | 含义           | 参数类型                                  |
| -------------------------------------- | -------------- | ----------------------------------------- |
| `MODIFIER_UNIT_ADJUST_PROPERTY`        | 改变单位的属性 | Key（字符串）和Amount（整数）             |
| `MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH` | 改变单位战斗力 | Key（字符串），或是Amount（整数），二选一 |

吸血鬼之所以能够可变加力，是因为`MODIFIER_UNIT_ADJUST_PROPERTY`可以不断增加其某项属性，而`MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH`可以读取属性，并把属性对应的值作为增加战斗力的值。`MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH`是非常特殊的一种`Modifier`，其参数的写法有两种，要么写Property的`Key`，要么直接写一个`Amount`。

那么，怎么利用这个机制来实现动态加力呢？

##### 5.3.1 纯Modifier的做法

首先，您可以采用纯`Modifier`形式。首先组装一个`ModifierType`

```xml
<GameData>
    <Types>
    	<Row Type="MODIFIER_PLAYER_UNITS_ADJUST_PROPERTY_TEST" Kind="KIND_MODIFIER"/>
	</Types>
	<DynamicModifiers>
		<Row>
			<ModifierType>MODIFIER_PLAYER_UNITS_ADJUST_PROPERTY_TEST</ModifierType>
			<CollectionType>COLLECTION_PLAYER_UNITS</CollectionType>
			<EffectType>EFFECT_ADJUST_UNIT_PROPERTY</EffectType>
		</Row>
    </DynamicModifiers>
</GameData>
```

然后对玩家的单位改属性，限制是某种单位类型

```xml
<GameData>
    <Modifiers>
        <Row>
            <ModifierId>TEST_UNIT_X_ADJUST_PROPERTY</ModifierId>
            <ModifierType>MODIFIER_PLAYER_UNITS_ADJUST_PROPERTY_TEST</ModifierType>
            <SubjectRequirementSetId>REQSET_UNIT_IS_X</SubjectRequirementSetId>
        </Row>
    	<!--其中REQSET_UNIT_IS_X可以包含一个条件类型是REQUIREMENT_UNIT_TYPE_MATCHES的条件，或者其他可用在单位上的条件--> 
    </Modifiers>
    
	<ModifierArguments>
        <Row>
            <ModifierId>TEST_UNIT_X_ADJUST_PROPERTY</ModifierId>
            <Name>Key</Name>
            <Value>COMBAT_STRENGTH_FOR_UNIT_X</Value>
        </Row>
        <!--起一个不容易撞车的Key名字--> 
        <Row>
			<ModifierId>TEST_UNIT_X_ADJUST_PROPERTY</ModifierId>
			<Name>Amount</Name>
			<Value>2</Value>
		</Row>
        <!--每个Modifier加几点属性，Amount就写几-->
	</ModifierArguments>
</GameData>
```

如果我们希望实现，每个纪念碑为`UNIT_X`这种单位加2点战斗力（改变几点属性），就挂给纪念碑的`BuildingModifier`。挂给玩家的其他对象也可以，比如区域，城市，改良等等。

```xml
<GameData>
	<BuildingModifiers>
		<Row>
			<BuildingType>BUILDING_MONUMENT</BuildingType>
			<ModifierId>TEST_UNIT_X_ADJUST_PROPERTY</ModifierId>
		</Row>
	</BuildingModifiers>
</GameData>
```

单单改变属性还不够，我们需要为这种单位写一个特殊的绑定到单位类型的`AbilityClass`的`Tag`，以及一个和这个`Tag`绑定的`UnitAbility`

```xml
<GameData>
	<Tags>
		<Row Tag="CLASS_TEST_X" Vocabulary="ABILITY_CLASS"/>
	</Tags>
	<TypeTags>
		<Row Type="UNIT_X" Tag="CLASS_TEST_X"/>	
		<Row Type="ABILITY_TEST_X" Tag="CLASS_TEST_X"/>
	</TypeTags>
    <UnitAbilities>
		<Row UnitAbilityType="ABILITY_TEST_X" Name="LOC_ABILITY_TEST_X_NAME" Description="LOC_ABILITY_TEST_X_DESCRIPTION"/>
	</UnitAbilities>
	<UnitAbilityModifiers>
		<Row>
			<UnitAbilityType>ABILITY_HULCHE</UnitAbilityType>
			<ModifierId>HULCHE_BONUS_VS_WOUNDED_UNITS</ModifierId>
		</Row>
    </UnitAbilityModifiers>
    
    <Modifiers>
        <Row>
            <ModifierId>TEST_UNIT_X_ADJUST_COMBAT_STRENGTH</ModifierId>
            <ModifierType>MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH</ModifierType>
        </Row>
    </Modifiers>
    
	<ModifierArguments>
        <Row>
            <ModifierId>TEST_UNIT_X_ADJUST_COMBAT_STRENGTH</ModifierId>
            <Name>Key</Name>
            <Value>COMBAT_STRENGTH_FOR_UNIT_X</Value>
        </Row>
        <!--Key名字同上--> 
	</ModifierArguments>
    
     <!--最后写战斗预览信息-->
    <ModifierStrings>
		<Row ModifierId="TEST_UNIT_X_ADJUST_COMBAT_STRENGTH" Context="Preview" Text="TEST_UNIT_X_ADJUST_COMBAT_STRENGTH_DESC"/>
	</ModifierStrings>
</GameData>

```

其中，战斗预览文本需要这样写

```xml
<GameData>
    <LocalizedText>
		<Replace Tag="TEST_UNIT_X_ADJUST_COMBAT_STRENGTH_DESC" Language="zh_Hans_CN">
      		<Text>+{Property}来自单位x的增益</Text>
    	</Replace>
	</LocalizedText>
</GameData>
```



##### 5.3.2 使用lua的做法

使用lua的做法，其实就是把使用`MODIFIER_UNIT_ADJUST_PROPERTY`改变单位属性换成直接用lua设置属性，其他部分（也就是单位能力部分）不变

比如，我们希望中国文明在遇到新文明后，单位X加2战斗力，可以这样写

```lua
function On_SetUnitProperty(playerID)
	--设置单位属性
    local pPlayer = Players[playerID]
    for i, unit in pPlayer:GetUnits():Members() do
        if (unit ~= nil) and (unit:GetType() == GameInfo.Units["UNIT_X"].Index) then
            local old_property = unit:GetProperty("COMBAT_STRENGTH_FOR_UNIT_X")
         	local new_property = 0
            if old_property == nil then
                new_property = 2
            else
                new_property = old_property + 2
            end
            unit:SetProperty("COMBAT_STRENGTH_FOR_UNIT_X", new_property)
        end
    end
end

function MeetCivilization(player1ID, player2ID)
	local pPlayer_1 = Players[player1ID]
	local pPlayer_2 = Players[player2ID]

	if pPlayer_1:IsMajor() then
		local playerConfig_1 = PlayerConfigurations[player1ID];
		local pCiv_1 = playerConfig_1:GetCivilizationTypeName();
        --如果拥有朝代更替
		if CivilizationHasTrait(pCiv_1, 'TRAIT_CIVILIZATION_DYNASTIC_CYCLE') then
			On_SetUnitProperty(player1ID)
		end
	end
	if pPlayer_2:IsMajor() then
		local playerConfig_2 = PlayerConfigurations[player1ID];
		local pCiv_2 = playerConfig_2:GetCivilizationTypeName();
		if CivilizationHasTrait(pCiv_2, 'TRAIT_CIVILIZATION_DYNASTIC_CYCLE') then
			On_SetUnitProperty(player2ID)
		end
	end
end

Events.DiplomacyMeet.Add( MeetCivilization )
```



#### 5.4 更方便地实现lua能力和特定玩家、城市或单位的绑定

其实，我们只要使用这些特殊的Modifier，就可以对玩家、城市或单位设置属性，在lua端检测这些属性即可完成绑定。

特殊Modifier列举如下（您也可以自组装，比如组装一个对玩家所有城市设置属性的Modifier）：

| 名称                                   | 含义               | 备注                                                   |
| -------------------------------------- | ------------------ | ------------------------------------------------------ |
| `MODIFIER_PLAYER_ADJUST_PROPERTY`      | 对玩家设置属性     | 通常挂给Trait、总督、政策（这种唯一的对象）            |
| `MODIFIER_SINGLE_CITY_ADJUST_PROPERTY` | 对单个城市设置属性 | 可以挂在区域、建筑、改良上（这种绑定在某个城市的对象） |
| `MODIFIER_UNIT_ADJUST_PROPERTY`        | 对单个单位设置属性 | 通常挂给单位能力                                       |

在lua端检测玩家有没有属性：

```lua
function my_test_func(playerID)
    local pPlayer = Players[playerID];
    local property = pPlayer:GetProperty("My_Property_Key")
    if property == nil then
        --如果不是目标玩家直接返回
        return
    end
end
Events.xx.Add(my_test_func)
```

检测单位和城市也类似，不再赘述。



#### 5.5 弹出确认框

只需要在UI环境写这些代码（感谢猭音的混沌入侵和巴巴洛丝mod的lua代码）

```lua
include("InstanceManager");
include("PopupDialog");
--确认框的类和相关的方法定义在这个官方文件中

--这个命名最好加上标识符
local m_kPopupDialog = PopupDialog:new( "APopupDialog" )
function callbackPopup()
	m_kPopupDialog:Close();
	UIManager:DequeuePopup(ContextPtr);
end

function showDialog(text)
    --text就是你要显示的文本，记得查询本地化文本
	local str = Locale.Lookup(text);
	m_kPopupDialog:Close();
	m_kPopupDialog:ShowOkDialog(str, function() callbackPopup(); end);
    --第二个参数代表优先级，可以填很多不同的值，可以去搜官方文件
	UIManager:QueuePopup( ContextPtr, PopupPriority.Utmost );
end
```



#### 5.6 使用事件框架（先咕咕咕）



### Chapter6:  新增UI详解

#### 6.1 认识常用的UI控件

常用的UI控件见下表：

| 名称          | 含义                                     | 备注 |
| ------------- | ---------------------------------------- | ---- |
| `GridButton`  | 方形按钮                                 |      |
| `Button`      | 普通按钮                                 |      |
| `Stack`       | 一种容器，用来放置和排列不同的控件       |      |
| `Label`       | 标签，用来显示文字                       |      |
| `Grid`        | 布局，一种容器，用来放置和排列不同的控件 |      |
| `Container`   | 一种容器，用来放置和排列不同的控件       |      |
| `Image`       | 图片，显示背景或图标等                   |      |
| `CheckBox`    | 选择框，用来单选                         |      |
| `PullDown`    | 下拉框，用来多选                         |      |
| `Box`         | 似乎常常用作背景                         |      |
| `ScrollPanel` | 滚动面板                                 |      |
| `ScrollBar`   | 滚动条                                   |      |
| `SlideAnim`   | 动画，常用在界面切换处，可用来做计时器   |      |
| `AlphaAnim`   | 动画，淡入淡出？                         |      |
| `FlipAnim`    | 动画                                     |      |
| `EditBox`     | 编辑框，常用在搜索                       |      |
| `Meter`       | 环形进度条（比如科技树进度）             |      |
| `Bar`         | 线型进度条（比如招募伟人进度）           |      |
| `Slider`      | 线型滑块（比如选择几个城邦那个水平滑块） |      |
| `Tutorial`    | 很神奇，没理解作用                       |      |



常见的控件属性如下表：

| 名称 | 含义 | 备注 |
| ---- | ---- | ---- |
|      |      |      |



常用的控件操作见下表：

| 名称                  | 含义                       | 备注                     |
| --------------------- | -------------------------- | ------------------------ |
| `:SetDisabled()`      | 设置是否可用               |                          |
| `:SetEnabled()`       | 设置是否可用               | 怎么感觉功能有点重复     |
| `:SetHide()`          | 设置可见性                 |                          |
| `:SetSelected()`      | 设置（看起来）是否选中控件 |                          |
| `:SetCheck()`         | 设置是否选中选择框         | 用于`CheckBox`           |
| `:SetToBeginning()`   | 重播动画，回到起点         | 用于动画                 |
| `:SetToEnd()`         | 动画终点                   | 用于动画                 |
| `:SetTexture()`       | 设置材质                   |                          |
| `:SetIcon()`          | 设置图标                   |                          |
| `:SetToolTipString()` | 设置鼠标悬停提示           |                          |
| `:SetText()`          | 设置文本                   |                          |
| `:SetSizeY(）`        | 设置控件Y大小              |                          |
| `:SetSizeX(）`        | 设置控件X大小              |                          |
| `:SetPercent()`       | 设置环形进度条的百分比     | 用于`Meter/Bar`等        |
| `:SetProgress()`      | 设置动画进度？             | 用于`AlphaAnim`等        |
| `:SetColor()`         | 设置颜色                   |                          |
| `:SetColorByName()`   | 设置颜色                   |                          |
| `:SetVoid1()`         | 用来给回调函数传参，参数1  |                          |
| `:SetVoid2()`         | 用来给回调函数传参，参数2  |                          |
| `:SetVoids( a, b)`    | 用来给回调函数传参         | 似乎可以一次设置多个参数 |
| `:SetFontSize()`      | 设置字号                   |                          |
| `:SetOffsetVal(x, y)` | 设置偏移量                 |                          |
| `:SetOffsetX()`       | 设置X偏移量                |                          |
| `:SetOffsetY()`       | 设置Y偏移量                |                          |
| `:SetScrollValue()`   | 设置滚动条位置？           |                          |
| `:SetAlpha()`         | 设置透明度？               |                          |
| `:SetAnchor("C,C")`   | 设置对齐方式               |                          |

常用的绑定回调函数方式：

| 方式                                           | 含义     |      |
| ---------------------------------------------- | -------- | ---- |
| `:RegisterCallback( Mouse.eLClick, func）`     | 左键     |      |
| `:RegisterCallback( Mouse.eRClick, func）`     | 右键     |      |
| `:RegisterCallback( Mouse.eMouseEnter, func）` | 鼠标进入 |      |
|                                                |          |      |

除此之外，控件还有很多独有的方法，我们挑选一些在6.2节讲解。



#### 6.2 新增UI实战一：固定的控件

##### 6.2.1 简单的按钮触发函数

##### 6.2.2 下拉框的使用

#### 6.3 新增UI实战二：动态生成控件/实例（Instance）



### Chapter7:  地图生成脚本专题

首先，我们要了解地图的分类，官方的地图主要分为两种，也就是随机地图和静态地图。随机地图是指通过.lua文件生成的地图，比如大陆地图、群岛地图等。静态地图是指从.Civ6Map文件加载的地图，其中的地形地貌资源等等都是固定的，比如四叶草、真实欧洲地图等等。

创意工坊的YNMAP模组引入了新的地图类型，其原理和官方非常不一样，我们先不讨论其内容。

#### 7.1 随机地图

##### 7.1.1 随机地图的定义和加载方式

我们来讨论随机地图，假设我们要新增一种风云变幻地图，叫“大陆-真实河流”，那么我们需要先在.xml或.sql注册

```xml
<GameData>
    <Maps>
            <Row Domain="Maps:Expansion2Maps" File="Real_Rivers_Continents.lua" Name="LOC_MAP_REAL_RIVERS_NAME" Description="LOC_MAP_REAL_RIVERS_DESCRIPTION" Image="Map_Continents" SortIndex="60"/>
    </Maps>

    <Parameters>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="WorldAge" Name="LOC_MAP_WORLD_AGE_NAME" Description="LOC_MAP_WORLD_AGE_DESCRIPTION" Domain="WorldAge" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="world_age" GroupId="MapOptions" Hash="0" SortIndex="230"/>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="Temperature" Name="LOC_MAP_TEMPERATURE_NAME" Description="LOC_MAP_TEMPERATURE_DESCRIPTION" Domain="Temperature" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="temperature" GroupId="MapOptions" Hash="0" SortIndex="240"/>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="Rainfall" Name="LOC_MAP_RAINFALL_NAME" Description="LOC_MAP_RAINFALL_DESCRIPTION" Domain="Rainfall" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="rainfall" GroupId="MapOptions" Hash="0" SortIndex="250"/>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="SeaLevel" Name="LOC_MAP_SEA_LEVEL_NAME" Description="LOC_MAP_SEA_LEVEL_LOW_DESCRIPTION" Domain="SeaLevel" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="sea_level" GroupId="MapOptions" Hash="0" SortIndex="260"/>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="Resources" Name="LOC_MAP_RESOURCES_NAME" Description="LOC_MAP_RESOURCES_DESCRIPTION" Domain="Resources" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="resources" GroupId="MapOptions" Hash="0" SortIndex="270"/>
            <Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="StartPosition" Name="LOC_MAP_START_POSITION_NAME" Description="LOC_MAP_START_POSITION_DESCRIPTION" Domain="StartPosition" DefaultValue="2" ConfigurationGroup="Map" ConfigurationId="start" GroupId="MapOptions" Hash="0" SortIndex="280"/>
    </Parameters>
</GameData>
```

其中`Maps`表是注册地图，`Parameters`表是注册地图参数的。对于风云变幻地图来说，都需要纪元、温度、降雨量、海平面、资源、起始位置这些参数。

然后进行加载，地图要定义在`FrontEnd`中

```xml
<FrontEndActions>
    <UpdateDatabase id="config_database">
      <Properties>
        <LoadOrder>20000</LoadOrder>
      </Properties>
      <File>Configs/MapScripts.xml</File>
    </UpdateDatabase>
</FrontEndActions>
```

接下来进入到地图脚本部分。地图脚本需要在`InGame`加载

```xml
 <InGameActions>
 	<ImportFiles id="rr_if" criteria="RR_Expansion2">    
  		<Properties>
        	<LoadOrder>30000</LoadOrder>
      	</Properties>
        <File>Maps/Real_Rivers_Continents.lua</File>
    </ImportFiles>
</InGameActions>
```

##### 7.1.2 随机地图lua文件的结构

我们直接参考官方的大陆地图，也就是`Continents.lua`。它有两个版本，直接看风云变幻版本的。

```lua
------------------------------------------------------------------------------
--	FILE:	 Continents.lua
--	AUTHOR:  
--	PURPOSE: Base game script - Produces widely varied continents.
------------------------------------------------------------------------------
--	Copyright (c) 2014 Firaxis Games, Inc. All rights reserved.
------------------------------------------------------------------------------

--引用一系列基础生成器lua文件
include "MapEnums"
include "MapUtilities"
include "MountainsCliffs"
include "RiversLakes"
include "FeatureGenerator"
include "TerrainGenerator"
include "NaturalWonderGenerator"
include "ResourceGenerator"
include "CoastalLowlands"
include "AssignStartingPlots"

local g_iW, g_iH;
local g_iFlags = {};
local g_continentsFrac = nil;
local featureGen = nil;
local world_age_new = 5;
local world_age_normal = 3;
local world_age_old = 2;

-------------------------------------------------------------------------------
function GenerateMap()
    --这是地图的主函数
	print("Generating Continents Map");
	local pPlot;
	
    --获取地图大小、温度
	-- Set globals
	g_iW, g_iH = Map.GetGridSize();
	g_iFlags = TerrainBuilder.GetFractalFlags();
	local temperature = MapConfiguration.GetValue("temperature"); -- Default setting is Temperate.
	if temperature == 4 then
		temperature  =  1 + TerrainBuilder.GetRandomNumber(3, "Random Temperature- Lua");
	end
	
    --获取纪元
	--	local world_age
	local world_age = MapConfiguration.GetValue("world_age");
	if (world_age == 1) then
		world_age = world_age_new;
	elseif (world_age == 2) then
		world_age = world_age_normal;
	elseif (world_age == 3) then
		world_age = world_age_old;
	else
		world_age = 2 + TerrainBuilder.GetRandomNumber(4, "Random World Age - Lua");
	end
	
    --划分海陆
	plotTypes = GeneratePlotTypes(world_age);
    --根据温度生成地形
	terrainTypes = GenerateTerrainTypes(plotTypes, g_iW, g_iH, g_iFlags, false, temperature);
    --实际设置地形
	ApplyBaseTerrain(plotTypes, terrainTypes, g_iW, g_iH);

	AreaBuilder.Recalculate();
	TerrainBuilder.AnalyzeChokepoints();
	TerrainBuilder.StampContinents();

	local iContinentBoundaryPlots = GetContinentBoundaryPlotCount(g_iW, g_iH);
	local biggest_area = Areas.FindBiggestArea(false);
	print("After Adding Hills: ", biggest_area:GetPlotCount());
    --这个函数其实是做火山的逻辑的，神奇吧
    --根据纪元生成火山
	AddTerrainFromContinents(plotTypes, terrainTypes, world_age, g_iW, g_iH, iContinentBoundaryPlots);

	AreaBuilder.Recalculate();
	
    --生成河流，这和高度有关系
	-- River generation is affected by plot types, originating from highlands and preferring to traverse lowlands.
	AddRivers();
	
    --生成湖泊
	-- Lakes would interfere with rivers, causing them to stop and not reach the ocean, if placed any sooner.
	local numLargeLakes = GameInfo.Maps[Map.GetMapSize()].Continents;
	AddLakes(numLargeLakes);
	
    --根据降雨量生成地貌
	AddFeatures();
	TerrainBuilder.AnalyzeChokepoints();
	
    --生成悬崖
	print("Adding cliffs");
	AddCliffs(plotTypes, terrainTypes);
	
    --生成自然奇观
	local args = {
		numberToPlace = GameInfo.Maps[Map.GetMapSize()].NumNaturalWonders,
	};
	local nwGen = NaturalWonderGenerator.Create(args);
	
    --根据大陆生成地热和绿洲
	AddFeaturesFromContinents();
    --根据一定的打分算法标记出一些海岸低地
	MarkCoastalLowlands();
	
    --根据资源生成资源
	resourcesConfig = MapConfiguration.GetValue("resources");
	local startConfig = MapConfiguration.GetValue("start");-- Get the start config
	local args = {
		resources = resourcesConfig,
		START_CONFIG = startConfig,
	};
	local resGen = ResourceGenerator.Create(args);

	print("Creating start plot database.");
	
    --生成启动位置
	-- START_MIN_Y and START_MAX_Y is the percent of the map ignored for major civs' starting positions.
	local args = {
		MIN_MAJOR_CIV_FERTILITY = 150,
		MIN_MINOR_CIV_FERTILITY = 50, 
		MIN_BARBARIAN_FERTILITY = 1,
		START_MIN_Y = 15,
		START_MAX_Y = 15,
		START_CONFIG = startConfig,
	};
	local start_plot_database = AssignStartingPlots.Create(args)
	
    --生成村庄
	local GoodyGen = AddGoodies(g_iW, g_iH);
end

-------------------------------------------------------------------------------
function GeneratePlotTypes(world_age)
    --划分海洋与陆地的函数
	print("Generating Plot Types");
	local plotTypes = {};

	local sea_level_low = 57;
	local sea_level_normal = 62;
	local sea_level_high = 66;

	local extra_mountains = 0;
	local grain_amount = 3;
	local adjust_plates = 1.0;
	local shift_plot_types = true;
	local tectonic_islands = false;
	local hills_ridge_flags = g_iFlags;
	local peaks_ridge_flags = g_iFlags;
	local has_center_rift = true;
	local water_percent;
	
    --获取海平面，调整海洋占比
	--	local sea_level
    	local sea_level = MapConfiguration.GetValue("sea_level");
	if sea_level == 1 then -- Low Sea Level
		water_percent = sea_level_low
	elseif sea_level == 2 then -- Normal Sea Level
		water_percent =sea_level_normal
	elseif sea_level == 3 then -- High Sea Level
		water_percent = sea_level_high
	else
		water_percent = TerrainBuilder.GetRandomNumber(sea_level_high - sea_level_low, "Random Sea Level - Lua") + sea_level_low  + 1;
	end
	
    --获取纪元，调整丘陵和山脉占比
	-- Set values for hills and mountains according to World Age chosen by user.
	local adjustment = world_age;
	if world_age <= world_age_old  then -- 5 Billion Years
		adjust_plates = adjust_plates * 0.75;
	elseif world_age >= world_age_new then -- 3 Billion Years
		adjust_plates = adjust_plates * 1.5;
	else -- 4 Billion Years
	end
	
    --尝试生成地图，直到最大的一块陆地占总陆地的大小不超过0.6左右
	-- Generate continental fractal layer and examine the largest landmass. Reject
	-- the result until the largest landmass occupies 58% or less of the total land.
	local done = false;
	local iAttempts = 0;
	local iWaterThreshold, biggest_area, iNumTotalLandTiles, iNumBiggestAreaTiles, iBiggestID;
	while done == false do
		local grain_dice = TerrainBuilder.GetRandomNumber(7, "Continental Grain roll - LUA Continents");
		if grain_dice < 4 then
			grain_dice = 2;
		else
			grain_dice = 1;
		end
		local rift_dice = TerrainBuilder.GetRandomNumber(3, "Rift Grain roll - LUA Continents");
		if rift_dice < 1 then
			rift_dice = -1;
		end
		
        --第一个参数代表大陆数量，第二个参数代表裂缝数量
		InitFractal{continent_grain = grain_dice, rift_grain = rift_dice};
		iWaterThreshold = g_continentsFrac:GetHeight(water_percent);
		local iBuffer = math.floor(g_iH/13.0);
		local iBuffer2 = math.floor(g_iH/13.0/2.0);

		iNumTotalLandTiles = 0;
		for x = 0, g_iW - 1 do
			for y = 0, g_iH - 1 do
				local i = y * g_iW + x;
				local val = g_continentsFrac:GetHeight(x, y);
				local pPlot = Map.GetPlotByIndex(i);
                
                if(y <= iBuffer or y >= g_iH - iBuffer - 1) then
                	--如果是两极，则都是海洋
					plotTypes[i] = g_PLOT_TYPE_OCEAN;
					TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_OCEAN);  -- temporary setting so can calculate areas
				else
                	--如果是中间部分
					if(val >= iWaterThreshold) then
                    	--如果高度高于水平面，为陆地
						if(y <= iBuffer + iBuffer2) then
                            --如果是南半球的极地圈附近，则随机划分为陆地或海洋，基本是海洋，且越往南越容易为海洋
							local iRandomRoll = y - iBuffer + 1;
							local iRandom = TerrainBuilder.GetRandomNumber(iRandomRoll, "Random Region Edges");
							if(iRandom == 0 and iRandomRoll > 0) then
								plotTypes[i] = g_PLOT_TYPE_LAND;
								TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_DESERT);  -- temporary setting so can calculate areas
								iNumTotalLandTiles = iNumTotalLandTiles + 1;
							else 
								plotTypes[i] = g_PLOT_TYPE_OCEAN;
								TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_OCEAN);  -- temporary setting so can calculate areas
							end
						elseif (y >= g_iH - iBuffer - iBuffer2 - 1) then
                            --如果是北半球的极地圈附近，则随机划分为陆地或海洋，基本是海洋，且越往北越容易为海洋
							local iRandomRoll = g_iH - y - iBuffer;
							local iRandom = TerrainBuilder.GetRandomNumber(iRandomRoll, "Random Region Edges");
							if(iRandom == 0 and iRandomRoll > 0) then
								plotTypes[i] = g_PLOT_TYPE_LAND;
								TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_DESERT);  -- temporary setting so can calculate areas
								iNumTotalLandTiles = iNumTotalLandTiles + 1;
							else
								plotTypes[i] = g_PLOT_TYPE_OCEAN;
								TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_OCEAN);  -- temporary setting so can calculate areas
							end
						else
                            --一般情况就是陆地
							plotTypes[i] = g_PLOT_TYPE_LAND;
							TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_DESERT);  -- temporary setting so can calculate areas
							iNumTotalLandTiles = iNumTotalLandTiles + 1;
						end
					else
                    	--如果高度低于水平面，为海洋
						plotTypes[i] = g_PLOT_TYPE_OCEAN;
						TerrainBuilder.SetTerrainType(pPlot, g_TERRAIN_TYPE_OCEAN);  -- temporary setting so can calculate areas
					end
				end
			end
		end
		
        --做一些中心化，也就是说，尽量把水多的地方移动到地图边缘，使得地图看起来好看
		ShiftPlotTypes(plotTypes);
        --搞裂缝把大陆分开
		GenerateCenterRift(plotTypes);

		AreaBuilder.Recalculate();
		local biggest_area = Areas.FindBiggestArea(false);
		iNumBiggestAreaTiles = biggest_area:GetPlotCount();
		
        --避免最大的大陆占比过大
		-- Now test the biggest landmass to see if it is large enough.
		if iNumBiggestAreaTiles <= iNumTotalLandTiles * 0.64 then
			done = true;
			iBiggestID = biggest_area:GetID();
		end
		iAttempts = iAttempts + 1;
		
		-- Printout for debug use only
		-- print("-"); print("--- Continents landmass generation, Attempt#", iAttempts, "---");
		-- print("- This attempt successful: ", done);
		-- print("- Total Land Plots in world:", iNumTotalLandTiles);
		-- print("- Land Plots belonging to biggest landmass:", iNumBiggestAreaTiles);
		-- print("- Percentage of land belonging to biggest: ", 100 * iNumBiggestAreaTiles / iNumTotalLandTiles);
		-- print("- Continent Grain for this attempt: ", grain_dice);
		-- print("- Rift Grain for this attempt: ", rift_dice);
		-- print("- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -");
		-- print(".");
	end
	
	local args = {};
	args.world_age = world_age;
	args.iW = g_iW;
	args.iH = g_iH
	args.iFlags = g_iFlags;
	args.blendRidge = 10;
	args.blendFract = 5;
	args.extra_mountains = 5;
	mountainRatio = 8 + world_age * 3;
	plotTypes = ApplyTectonics(args, plotTypes);
	plotTypes = AddLonelyMountains(plotTypes, mountainRatio);

	return plotTypes;
end

function InitFractal(args)
    --做一些分形，其中包含高度信息

	if(args == nil) then args = {}; end

	local continent_grain = args.continent_grain or 2;
	local rift_grain = args.rift_grain or -1; -- Default no rifts. Set grain to between 1 and 3 to add rifts. - Bob
	local invert_heights = args.invert_heights or false;
	local polar = args.polar or true;
	local ridge_flags = args.ridge_flags or g_iFlags;

	local fracFlags = {};
	
	if(invert_heights) then
		fracFlags.FRAC_INVERT_HEIGHTS = true;
	end
	
	if(polar) then
		fracFlags.FRAC_POLAR = true;
	end
	
	if(rift_grain > 0 and rift_grain < 4) then
		local riftsFrac = Fractal.Create(g_iW, g_iH, rift_grain, {}, 6, 5);
		g_continentsFrac = Fractal.CreateRifts(g_iW, g_iH, continent_grain, fracFlags, riftsFrac, 6, 5);
	else
		g_continentsFrac = Fractal.Create(g_iW, g_iH, continent_grain, fracFlags, 6, 5);	
	end

	-- Use Brian's tectonics method to weave ridgelines in to the continental fractal.
	-- Without fractal variation, the tectonics come out too regular.
	--
	--[[ "The principle of the RidgeBuilder code is a modified Voronoi diagram. I 
	added some minor randomness and the slope might be a little tricky. It was 
	intended as a 'whole world' modifier to the fractal class. You can modify 
	the number of plates, but that is about it." ]]-- Brian Wade - May 23, 2009
	--
    
    --分形是基于Voronoi diagram的
    --沃罗诺伊图（Voronoi diagram）又叫狄利克雷镶嵌（Dirichlet tessellation）或者泰森多边形（Thiessen polygon）。沃罗诺伊图解决的问题实际上就是基于一组特定点将平面分割成不同区域，而每一区域又仅包含唯一的特定点，并且该区域内任意位置到该特定点的距离比到其它的特定点都要更近。
    
	local MapSizeTypes = {};
	for row in GameInfo.Maps() do
		MapSizeTypes[row.MapSizeType] = row.PlateValue;
	end
	local sizekey = Map.GetMapSize();

	local numPlates = MapSizeTypes[sizekey] or 4

	-- Blend a bit of ridge into the fractal.
	-- This will do things like roughen the coastlines and build inland seas. - Brian

	g_continentsFrac:BuildRidges(numPlates, {}, 1, 2);
end

function AddFeatures()
    --生成地貌
	print("Adding Features");
	
    --获取定义的降雨量参数
	-- Get Rainfall setting input by user.
	local rainfall = MapConfiguration.GetValue("rainfall");
	if rainfall == 4 then
		rainfall = 1 + TerrainBuilder.GetRandomNumber(3, "Random Rainfall - Lua");
	end
	
    --生成地貌
	local args = {rainfall = rainfall}
	featuregen = FeatureGenerator.Create(args);
	featuregen:AddFeatures(true, true);  --second parameter is whether or not rivers start inland);
end

function AddFeaturesFromContinents()
    --根据大陆生成地貌，其实就是地热和绿洲
	print("Adding Features from Continents");

	featuregen:AddFeaturesFromContinents();
end

function GenerateCenterRift(plotTypes)
	--一个生成裂缝的函数，用于把大块的陆地分开
    --内容被我删了

end
```

经过上述的例子，我们总结出主函数`GenerateMap`的以下顺序：

1. 确定海陆划分（大陆划分应该也是在这时候生成的）
2. 为陆地生成不同地形，如果愿意的话，可以在陆地边缘多扩展几格浅海
3. 生成火山
4. 生成河流
5. 生成湖泊
6. 生成地貌
7. 生成悬崖
8. 生成自然奇观
9. 生成特殊地貌，比如地热（在大陆交界处），绿洲（不与其他地貌相邻）
10. 标记海岸低地
11. 生成资源
12. 生成启动位置
13. 生成村庄

在整个地图文件中，除了主函数必须叫`GenerateMap`，我们可以定义和各种`Generator`中函数同名的函数来替代其功能，这是一种函数重载。如果不理解的话，多看看官方的不同随机地图就能看懂。

必要时，您也可以使用`ImportFiles`的方式，直接替代官方的基础`Generator`。

真实河流地图，其实就是在2和3之间加入了大河地形，在9和10之间加入了大河中的特殊地貌。

#### 7.2 静态地图

先略

#### 7.3 地图/游戏参数传递到游戏中，实现特殊功能

##### 7.3.1 地图参数的定义和数据类型

在7.1节，我们已经看到了地图参数在地图生成中的作用，比如

```lua
local world_age = MapConfiguration.GetValue("world_age");
```

至于参数的数据类型，和`Domain`有关 。您需要了解一些`Parameters`表和`DomainValues`表的一些基本知识。简单来说，前者需要选择一种Domain，需要在后者中定义。

一般，地图参数的数据类型都是`number`。

下面展示了新增一种地图参数，大河长度

```xml
<Parameters>	
		<!--河流最长长度-->
		<Row Key1="Map" Key2="Real_Rivers_Continents.lua" ParameterId="RealRiverMaxLength" 	Name="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME"	Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_DESCRIPTION"	Domain="RealRiverMaxLength_Domain" 	DefaultValue="12" 	ConfigurationGroup="Map" ConfigurationId="RealRiver_MaxLength"	GroupId="MapOptions" SortIndex="300"/>
</Parameters>

<DomainValues>
		<Replace Domain="RealRiverMaxLength_Domain" Value="6" 		Name="6" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="0"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="9" 		Name="9" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="5"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="12" 		Name="12" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="7"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="15" 		Name="15" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="10"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="18" 		Name="18" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="20"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="21" 		Name="21" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="30"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="24" 		Name="24" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="40"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="27" 		Name="27"		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="50"/>
		<Replace Domain="RealRiverMaxLength_Domain" Value="30" 		Name="30" 		Description="LOC_MAP_REAL_RIVER_MAX_LEGNTH_NAME" 	SortIndex="60"/>
</DomainValues>DomainValues>
```



##### 7.3.2 参数实现特殊功能：直接lua调用

最杰出的典范之一，就是Ophidy的自选奇观开局mod。

| 参数类型 | ConfigurationGroup | GroupId     | 引用方法                                      |
| -------- | ------------------ | ----------- | --------------------------------------------- |
| 地图参数 | Map                | MapOptions  | `MapConfiguration.GetValue(ConfigurationId)`  |
| 游戏参数 | Game               | GameOptions | `GameConfiguration.GetValue(ConfigurationId)` |

注意，领袖池1/2、城邦、自然奇观，这4个参数的数据类型比较特殊，是`Array="1"`，是一个表。并且这种类型的参数`Domain`无法新增，只能套用！！！

自选奇观开局mod的实质，就是在分配出生点的时候调用自定义的参数，强行分配出生点。

除此之外，还有一个类似的，天生城邦能力的mod，是调用参数，查询城邦宗主国对应的`Modifier`，然后`AttachModifierByID`。



##### 7.3.2 参数实现特殊功能：选择性加载，criteria

这其实和游戏模式的选择性加载类似，因为游戏模式其实就是一种特殊的`Parameter`。

您可以看我的无限文明mod，其中广泛用到了选择性加载。

举例如下，注意多个条件之间是`and`关系

```xml
 <ActionCriteria>
     	<!-- 两个游戏模式都开启时 -->
		<Criteria id="RR_TowerDefense_Func_Mode">
			<ConfigurationValueMatches>
				<Group>Game</Group>
				<ConfigurationId>GAMEMODE_TOWERDEFENSE</ConfigurationId>
				<Value>1</Value>
			</ConfigurationValueMatches>
			<ConfigurationValueMatches>
				<Group>Game</Group>
				<ConfigurationId>GAMEMODE_REAL_RIVERS_FUNC</ConfigurationId>
				<Value>1</Value>
			</ConfigurationValueMatches>
		</Criteria>
     	<!-- 真实河流模式开启和越南包启用时 -->
		<Criteria id="RR_KublaiKhan_Vietnam_Func_Mode">	
			<ConfigurationValueMatches>
				<Group>Game</Group>
				<ConfigurationId>GAMEMODE_REAL_RIVERS_FUNC</ConfigurationId>
				<Value>1</Value>
			</ConfigurationValueMatches>
			<ModInUse>A3F42CD4-6C3E-4F5A-BC81-BE29E0C0B87C</ModInUse>
		</Criteria>	
  </ActionCriteria>

  <InGameActions>
	<UpdateDatabase id="ingame_database_towerdefense_func_mode" criteria="RR_TowerDefense_Func_Mode">
	  <Properties>
        <LoadOrder>30210</LoadOrder>
      </Properties>
	  <File>Data/GameMode/RR_Improvements_TowerDefense_Mode.sql</File>
    </UpdateDatabase>
	<UpdateDatabase id="ingame_database_KublaiKhan_Vietnam_func_mode" criteria="RR_KublaiKhan_Vietnam_Func_Mode">
	  <Properties>
        <LoadOrder>30220</LoadOrder>
      </Properties>
	  <File>Data/GameMode/RR_Firaxis_Support_KublaiKhan_Vietnam.sql</File>
    </UpdateDatabase>
  </InGameActions>
```



