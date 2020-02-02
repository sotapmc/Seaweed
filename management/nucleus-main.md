# Nucleus 基础

服务器采用 Nucleus 作为基础管理插件，其功能类似于 Bukkit 服务器上的 Essentials 系列。

<img src="https://i.imgur.com/Gcgbmcj.png" style="display: block; margin:auto">

!> 当前页面所叙述的所有信息仅适用于服务器的管理员。

## 与 Essentials 的区别

Nucleus 很大程度上还原了 Essentials 的使用体验，即沿用了一些传统的指令，例如 `/tpa`、`/afk`、`/speed` 等，但值得注意的是在这其中还增添了一些特殊的成分。

- Nucleus 内对于时间的设定并不含 `/day`、`/night` 等简写，也就是说如果想要调整至日间或夜间，必须规范使用 `/time set day` 或 `/time set night`；
- Nucleus 的传送功能被一分为三。区别为 Essentials 只有一个 `/tp`，Nucleus 将这个指令的功能分成了三份，分别是：
    - `/tp <玩家>` **传送至**，即只能传送到指定玩家的地方，不可继续跟任何参数；
    - `/tphere <玩家>` **传送来**，即只能传送指定玩家到此处。在 Essentials 中，使用的是 `/tp <玩家 A> <玩家 B>`；
    - `/tppos <坐标>` **传送至坐标**，即只能传送至指定的坐标。在 Essentials 中，`/tp` 后可直接跟坐标。
- Nucleus 对权限的划分比 Essentials 明确*，详见条目「[#权限](#权限)」；
- Nucleus 自带多世界功能，这是 Essentials 所不具有的，详见条目「[#多世界](#多世界)」；
- Nucleus 自带称号功能，且可搭配 LuckPerm 进行配置，详见条目「[#称号](#称号)」；
- **`/help` 指令不是 Nucleus 指令，而是 Sponge 指令，需要单独配置 Sponge 的权限节点。**

如果发现了更多区别，可以在 GitHub 上提出修改意见。我们需要将这些区别罗列在这里，以便那些熟悉了 Bukkit 服务器生态的管理员能够迅速上手 Nucleus。

## 权限

Nucleus 的主权限节点是 `nucleus`，对于每一个指令，Nucleus 都有详细的节点分布。

对于一个指令的基础使用，Nucleus 常常采用 `command.base` 格式命名节点；对于一个指令的子指令，则继续在该指令的节点向下分叉。这些都可以很简单地用 LuckPerms 设置。

!> 需要注意的是，许多指令只有部分功能能够赋予普通玩家，例如 `nucleus.warp.base` 允许玩家使用 `/warp` 进行传送，而 `nucleus.warp.set` 则允许玩家设定 Warp（`/setwarp`）。因此在设置指令的时候**切勿直接给予子节点的全局权限**（例如 `nucleus.warp`）。

### 需要注意的

同时，参照官方文档，Nucleus 的权限设置过程中有一个巨大的「坑」：

> Yeah, 1000 permission nodes can be daunting, and there is nothing stopping you, but we honestly recommend you do not. If you do so, the following things will happen:
> - You will not be able to go AFK (permission: `nucleus.afk.exempt.toggle`)
> - Logging in/out of your server will never generate a connection message (permission: `nucleus.connectionmessages.disable`)
> - From 1.2 - logging in to the server will cause you to vanish (permission: `nucleus.vanish.onlogin`)
> - From 1.3 - you’ll give yourself the keep inventory permission (permission: `nucleus.inventory.keepondeath`)

因此无论何时均不要给予 **Wildcard（泛匹配）**权限，这样可能导致无意间就给上了一些根本没有达到目的，甚至造成负面影响的权限。例如 `nucleus.vanish.onlogin` 会导致每一次登录服务器都进入 Vanish 状态，进而除了聊天以外的任何方式均找不到玩家。

### 元数据

借助权限管理插件（例如 LuckPerms），我们可以直接设置元数据来完成一些较为简单的插件配置（而不需要修改配置文件）。

例如可以通过修改叫做 `home-count` 的元数据直接控制一个主体最多设置家的数量：

```minecraft
/lp <object> <object-name> meta set home-count <count>
```

其官方动用「元数据」方式的理由大概就是

> This is implemented as the permission option/meta `home-count`, rather than a specific permission **for performance reasons**. 

## 多世界

Nucleus 内置多世界功能，免去了额外安装的麻烦，功能基本够用。其主指令为 `/world`。以下是一些较为简单的用法：

|指令|解释|
|:-:|:-:|
|`/world list`|显示存在的世界列表|
|`/world tp <世界名>`|传送到指定的世界|
|`/world create <世界名> ...`|创建一个新的世界|
|`/world unload <世界名>`|卸载一个世界|
|`/world delete <世界名>`|删除一个世界|
|`/world rename <世界名> <新名称>`|重命名一个世界|
|`/world`|查看帮助|

在创建世界的时候，可以依靠参数添加一些生成器模板，例如生成基本世界则可以使用 `/world create world -p overworld`，生成空岛则是 `/world create skyland -p sky_land`。除此之外还可以在创建的时候预设难度、游戏规则、模式等，这些可以通过 `/world create ?` 获取帮助。

在删除世界的时候，需要确保如下三点

- 你不处于那个世界
- 那个世界已经被 `unload`
- 那个世界不是主世界（即 `server.properties` 内规定的 `level-name`）

## 称号

Nucleus 内置的称号功能很简洁（当然你可以通过配置[让它变得很复杂](https://nucleuspowered.org/docs/modules/chat.html)），同样依靠权限管理插件对元数据的修改（~~这两个好像是一对啊...?~~）。

对于 LuckPerms，添加前缀和后缀通常使用如下方式：

```minecraft
/lp <object> <object-name> meta addprefix <priority> <prefix>
```

添加后缀，将 `addprefix` 改成 `addsuffix` 即可。

`<priority>` 是对多个称号的存在所设计的，如果能够确保只会有一个称号，那么随便填写一个数字即可（不过最好为 `0`）；对于 `<prefix>` 的内容，需要注意的是默认情况下并不会有任何符号修饰。

举个例子，对于如下情况：

```chat
Subilan: blablabla
/lp user Subilan meta addprefix 0 thisisaprefix
```

那么你将得到的是

```chat
thisisaprefix Subilan: blablabla
```

而一般服务器的前缀都应该长这样：`[我是称号]` 或者 `<我是称号>`，甚至 `>>我是称号<<`，更甚至`*★,°*:.☆我是称号*.°★* 。`。总之不会有人去用这种没有任何修饰的前缀，否则就不应该叫做「称号」了。

所以我们需要在设置的时候手动添加这些东西。当然，这也并不是什么很耗费劳动力的东西，在这里也只是提及一下。

!> 如果我们要给称号设置颜色的话，要记得在写第二部分符号的时候应动用 `&f` 或 `&r` 进行还原。

```chat
/lp user Subilan meta addprefix 0 [&a我是称号&r]
```

这样显示出来的结果才是 <span style="background-color: black"><span style="color:white">[</span><span style="color:chartreuse">我是称号</span><span style="color:white">]</span></span>，而 `[&a我是称号]` 显示的却是 <span style="background-color: black"><span style="color:white">[</span><span style="color:chartreuse">我是称号]</span></span>

## 礼包

!>  如果您正在寻找**如何使用礼包**，请移步[这里](/player/basic-commands.md)

礼包也是 Nucleus 的组件之一。不同于传统礼包插件，Nucleus 的礼包不需要通过修改配置文件来实现，而是只需要在服务器内执行相应指令即可。

### 如何创建礼包

有两种办法，可以完全按照自己的意愿选择。

#### 箱子风格

执行指令 `/kit create <name>`，其中 `<name>` 为创建的礼包名称。执行以后，将会打开一个类似于箱子的 GUI（就相当于是打开箱子以后的画面），此时向里面放入礼包内容即可，退出以后，礼包将会被保存。

#### 直接创建

执行指令 `/clear` 清空自己的现有背包。然后在自己的背包里放入礼包的内容，需要注意的是放入物品的附魔、数量、名称等也会被记入到礼包里。放入完毕后，执行 `/kit add <name>` 即可创建一份礼包。

### 礼包指令

单单创建礼包是远远不够的，我们还需要一些拓展。以下列出了较为完整的礼包指令用法和解释。

|指令|解释|
|:-:|:-:|
|`/kit setcooldown <name> <time>`|指定某个礼包的冷却时长<sup>1</sup>|
|`/kit onetime <name> <true\|false>`|设置礼包是否为一次性（一个玩家只能领取一次）<sup>2</sup>|
|`/kit cost <name> <price>`|设置礼包价格（付费礼包）|
|`/kit edit <name>`|打开箱子 GUI 编辑某一礼包的内容|
|`/kit set <name>`|把自己当前背包的内容设置成一个现有礼包的内容（也就是修改）|
|`/kit remove <name>`|删除礼包|
|`/kit resetusage <player> <name>`|立即撤销某个玩家的某个礼包的冷却时间或者一次性<sup>3</sup>|
|`/kit command add <name> <command>`|在礼包中附加指令，在玩家兑换时执行<sup>4</sup>|
|`/kit command <name>`|查看某个礼包所包含的附加指令<sup>5</sup>|
|`/kit command remove <name> <index>`|删除某个礼包的附加指令<sup>6</sup>|
|`/kit setfirstlogin <name> true`|将某个已设置好的礼包设为新人礼包，新人进入服务器自动发放|

#### 注释

1. 冷却时长是指领取过一遍后需要等待多少时间才能再次领取。
    - `<time>` 参数的格式为 `数字+单位`
        - 例如 `10s` 代表 10 seconds（十秒），`20min` 代表 20 minutes（二十分钟），`30h` 代表 30 hours（三十小时），`40d` 代表 40 days（四十天）。**请不要使用年，因为服务器根本没有可能活那么久！**
2. 其中，最后一个参数 `<true|false>` 中，`true` 代表启用，`false` 代表禁用。
    - 例如 `/kit onetime example true` 代表将名为 `example` 的礼包设置为一次性的。
    - 例如 `/kit onetime example false` 代表取消名为 `example` 的礼包的一次性，使它可以被重复领取。
3. 执行该指令后，玩家可以立即领取这个礼包而无需等待。对于那些玩家已经领取过而导致失效的一次性礼包，在执行指令以后仍然可以再次领取。
4. 参数 `<command>` 就是指令的内容，可以在指令中夹杂 `{{player}}` 来代表玩家的名称。 
    - 例如 `/kit command add example suicide` 代表当玩家兑换 `example` 礼包的时候，自动执行 `/suicide`（自杀）。
    - 例如 `/kit command add example broadcast {{player}} 兑换了一个礼包。` 代表当玩家兑换 `example` 礼包的时候，自动执行 `/broadcast {{player}} 兑换了一个礼包。`，此时 `{{player}}` 会被自动替换为兑换礼包玩家的名称。因此此时公屏上会出现 `xxx 兑换了一个礼包。` 字样。
        - 注释：`/broadcast` 是广播指令。
5. 执行以后会出现一个列表，里面就是附加指令。点击前面的 `X` 可以直接删除某个指令。指令前面黄色的数字序号就是 `index`（见注释 6）。
6. 删除时需要指定 `index`。`index` 可以在 `/kit command <name>` 中看到，例如 `/kit command remove example 1` 代表删除 `example` 礼包中序号为 1 的指令。

### 礼包权限

!> 阅读本节内容需要 LuckPerms 基础。如果您仍然不熟悉，可以跳转到[这里](/management/luckperms-main.md)。

在创建礼包的同时，我们还可以指定哪些玩家可以使用礼包。

每个礼包都有一个独立的权限节点：`nucleus.kits.<name>`，其中 `<name>` 是礼包的名称。如果我们设定了一个 VIP 专属礼包，那么可以通过如下指令将这个礼包授予 vip 组。

```minecraft
/lp group vip permission set nucleus.kits.vip
```

如果想要让某个玩家或组获得所有礼包的权限，那么可以给予 `nucleus.kits.*` 也就是 `nucleus.kits`。

如果要禁止玩家领取任何礼包，可以

- 撤销 `nucleus.kits.*` 权限：`/lp user <name> permission set nucleus.kits.* false`
- 或者撤销 `nucleus.kits.base` 权限：`/lp user <name> permission set nucleus.kits.base false`

其中前者是撤销对所有**现有**礼包的访问权限，在这之后添加的礼包仍然可以领取。后者是撤销使用 `/kits` 指令的权限，这样无论什么时候创建的礼包都无法领取。
 
