# LuckPerms 基础

**LuckPerms** 是当今 Minecraft 插件界最为流行的权限管理插件之一，也是本服务器所采用的权限管理插件。LuckPerms 在服务端界可以算作**跨平台**的特例，即它对 Bukkit、Spigot、Sponge 多端均有原生支持。

![68747470733a2f2f692e696d6775722e636f6d2f546f6775466b512e706e67.png](https://i.loli.net/2020/01/16/CgeJUV9tR6p5zqK.png)

!> 当前页面所叙述的所有信息仅适用于服务器的管理员。

## 指令

LuckPerms 中最常见的指令结构如下：

```minecraft
/lp <object> <object-name> <controller> <...>
```

但是这个非常模糊，我们需要把它细化再加以解释。

### 操作主体 `<object>`

LuckPerms 的每一个指令都需要有一个明确的操作对象，即当前指令所要影响的对象。操作主体分为「主体类型」和「主体名称」两部分，分别排在指令的最前两个位置。

主体类型包括两种，一种是 `group`（[权限组](#权限组)），一种是 `user`（用户）。在选择时，为了避免重名（即存在名为 `hello` 的组也存在名为 `hello` 的人），需要对它们加以区分，这就是主体类型的用途。

### 操作名称 `<object-name>`

操作名称即操作主体的名称。在前面已经选择了 `group` 或者 `user` 中的一种，接下来所需要的就是一个具体的对象。如果我们指定一个名为 `HelloWorld` 的对象，那么根据前面所设置的主体类型，指令便会将结果锁定在某一个确定的主体上。

例如

```minecraft
/lp user Subilan ...
```

所针对的主体就是**用户 Subilan**。

而

```minecraft
/lp group Subilan ...
```

所针对的主体却是一个叫做 **Subilan** 的 **组**。

当然这在现实中不可能出现，体会精神实质即可。

### 控制器 `<controller>`

!> **控制器**只是一种比喻的说法，实际上它们是 `/lp group` 或 `/lp user` 的子指令。

控制器可以简单理解为所要使用的功能部分。例如，要设置对象的权限，那么控制器应选择 `permission`；要设置对象的元数据，那么控制器应选择 `meta`，等等。

LuckPerms 中的控制器可以通过输入 `/lp group` 或 `/lp user` 查看，`group` 和 `user` 的控制器有略微的差别。

```output
> lp group

[LP] Group Sub Commands: (/lp group <group> ...)
> info
> permission
> parent
> meta
> editor
> listmembers - [page]
> setweight - <weight>
> setdisplayname - <name> [context...]
> showtracks
> clear - [context...]
> rename - <name>
> clone - <name>
```

每一个控制器都有一定的效用，在这里我们仅介绍 `permission`、`parent` 和 `meta`。

?> 下文中不再区分主体为 `group` 还是 `user`（介绍 `parent` 时除外）。

!> 在「控制器」下还有很多下属分支内容，例如 `/lp <object> <object-name> permission set` 中的 `set`。具体用法，可以输入指令到 `/lp <object> <object-name> permission` 后回车，这样就会显示该控制器下属的所有子指令了。在本文中不会再详细介绍这些子指令，而是仅介绍 `set` 一种较为常用的子指令。

### 权限 `permission`

权限即设置权限，利用此控制器可以对一个主体进行权限的设置，用法如下：

```minecraft
/lp <object> <object-name> permission set <node> [true|false]
```

其中 `<node>` 为权限的节点名称，例如 `nucleus.vanish.base` 代表 `/vanish` 指令的基础权限，等等。每一个插件一般都会在自己的 Wiki 或发布帖上列出自己的权限节点列表，包括 Sponge、LuckPerms 本身。

最后的 `[true|false]` 为该节点的状态，只有两种，即 `true`（启用）或 `false`（禁用）。当该权限没有被插件作者默认设置，且尚未被配置时，它默认为 `false`。

?> 😎 如果最后的状态留空，那么会默认设置为 `true`，如果想要大范围地启用指令，可以留空不打 `true` 以节省时间。

?> 👀 一个个浏览节点文档不是一个好主意。我们也可以通过 Tab 补全来了解有哪些节点。一般来说，我们可以通过英文的字面意思猜出该节点的用途。例如，键入 `nucleus.r` 后 <kbd>Tab</kbd> 即可显示所有 Nucleus 插件下以 r 开头的权限节点。

### 继承 `parent`

继承即继承权限，也就是在上文中所提到过的继承。用于设置某一个主体继承另一个主体的权限。约定：A 继承 B，我们将 A 称为**子**，B 称为**父**。

通常用法如下：

```minecraft
/lp <object> <object-name> parent set <group-name>
```

其中，子方可以是 `user` 也可以是 `group`。如果是 `user`，那么就是将一个用户添加到一个组里；如果是 `group`，那么就是让一个组继承另一个组的所有权限。

父方也可以是 `user` 或 `group`，但一般情况下我们不会继承一个个体的权限，因此我们把它当作「只能为 `group` 看待」。

### 元数据 `meta`

元数据是对一个主体的根本配置，但实际上其作用性依赖于服务器整体的插件生态。官方 Wiki 是这样解释的：

> Sets a meta key value pair for a user/group. These values can be read and modified by other plugins using Vault or the Sponge Permissions API.

即元数据是 LuckPerms 提供的一个对外的接口，其它的插件可以通过 Sponge 的权限 API 或者 Vault API 读取它门。例如 Nucleus 中就可以直接使用 LuckPerms 改变玩家的家的数量，这就是一个经典的例子。

但是并不是每个插件都懂得去使用，因此对于不支持元数据接口的插件，这个功能可以说是形同虚设；但是一旦有插件使用了这个功能，那么就可以使所有对此插件的设置均在 LuckPerms 中借由元数据进行变为现实。

基本用法：

```minecraft
/lp <object> <object-name> meta set <key> <value>
```

其中 `<key>` 是元数据的数据名称，`<value>` 为其值。

### 上下文 `<context>`

上下文也是 LuckPerms 指令的一个参数，位于 LuckPerms 指令的最后：

```minecraft
/lp ... <context>
```

顾名思义，上下文即联系上下文，对某个设置起限定性的作用。上下文的格式为 `key=value`，例如 `server=main`、`world=zhucheng` 等。只有在上下文规定的条件成立时，该设置才有效。

例子：

```minecraft
/lp group default permission set nucleus.fly true world=zhucheng
```

这条指令的含义本是给予 `default` 组 `nucleus.fly` 的权限（即使用 `/fly` 指令），但是加上了 `world=zhucheng` 的上下文，代表只有在 `world` 属性为 `zhucheng` 的时候，该设置才会生效。

而 **`world` 属性为 `zhucheng`** 的实际含义就是「处于名为 `zhucheng` 的世界中」。也就是说，这个指令一旦真正在服务器里执行，那么所有玩家均可以在主城空岛上使用 `/fly` 指令飞行，但是在其它世界（如主世界）就会失效。


***

## 权限组

**权限组**是一种组概念，它可以包含多个用户个体，这被称为**继承**。

?> 🙋‍ 我们并不是专业的计算机技术讨论，所以我们会尽量避免使用这类名词。

一个权限组的地位与用户一样，可以设置它所拥有的权限、元数据等，这些设置均会应用到处于该组内的每一个用户身上。

默认情况下只有一个组，叫做 `default`（默认的意思），每一个新加入服务器的玩家都会被自动分配到这个组里，因此我们又称这个组为「**全局组**」，即对这个组所做的一切更改都会应用到服务器中的每个人身上。

而随着时间的推移，我们也许会设计出一些角色来，例如「管理员」、「建筑师」等。它们所需要的权限也不一样：我们不能给予建筑师关闭服务器的权限，也没必要给予普通玩家随意切换游戏模式的权限。因此我们需要把它们隔离出来，这就体现了「**组**」的作用。

我们可以额外创建这两个组，分别命名为 `op` 和 `builder` 组，然后再分别给这两个组恰当的权限，这样它们以及 `default` 就互不干扰了。

但是一个用户只能同时归属于一个组，而新建的组是没有任何权限的，我们难道要像当初设置 `default` 组那样重新设置一遍吗？这个时候我们就可以用到「**继承**」了。放心，这里的「继承」根本没有任何深层的技术含量。

继承是单向的，也就是说如果 A 继承 B 的权限，那么 A 会拥有 B 的所有权限，但 B 并不会拥有 A 的任何权限，这很好理解。也就是说，我们可以直接让新建的组继承 `default` 组，因为 `default` 里全部都是最基础的权限，人人都应该拥有。这样，新建的组便会拥有 `default` 的所有权限，而不要重新设置了。

然后，再在继承完毕的组的基础上加设权限，那么这些权限就只会存在于当前组，而不会对 `default` 造成任何影响。因此我们可以得出结论：**组与组之间，总是高级组继承低级组权限，这是一个不可逆的过程。**
