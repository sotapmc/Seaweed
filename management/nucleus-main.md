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