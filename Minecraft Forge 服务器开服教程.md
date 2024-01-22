# Minecraft Forge 服务器开服教程

[本文 GitHub](https://github.com/TowardtheStars/TowardtheStars-CSDN-Blogs/blob/main/Minecraft%20Forge%20%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%BC%80%E6%9C%8D%E6%95%99%E7%A8%8B.md)

@[TOC]

**这篇博文是给有知识有技能但还没学会开服的人看的**, 同时也是自己开服经验的记录。
如果你具备了下列的知识和技能，但没看懂，那是我写的不够清楚。

- 熟练使用命令行的基本命令，例如用 `cd` 切换当前文件夹等
- 知道 Java 和 JVM 的含义
- 熟悉 pip 包管理器，可以用 pip 安装、更新、删除 python 包 （MCDReforged 需要，非必须）
- 基本的英文阅读能力，至少要可以配合翻译软件看懂英语文档和日志文件
- 基本熟悉主要电脑硬件的名字和大概作用
- 熟悉 TCP/IP 协议的基础知识，例如 IP 地址、端口号、端口映射等
- 知道如何恰当地使用百度等搜索引擎寻找解决问题的方法
- 拥有足够的耐心阅读各种文档

## 资源准备

下列技能除 `MCDReforged 需要` 标注的以外，都是开一个 forge 服务器的必备资源。后面的笔记是基于使用 MCDReforged（下面简称 MCDR）开服记录的。
MCDR 是一个套在 Java 服务器外面的壳子，通过读取解析你服务器的控制台输出和插件来实现各种功能，例如：和 QQ 群、开黑啦服务器互通，自动热备份等。

### 硬件

- 一个具有较大上传带宽的网络（[网速测试站](https://www.speedtest.cn/)）
- 至少 4 GB 的 RAM
- 至少 50 GB 的硬盘空间

### 软件

- ~~Java 8，推荐 8u302（除了 8u321 和以后的版本其实都行）~~ Java 看下文
- Python 3.9+ (MCDReforged 需要)
- python 库 (MCDReforged 需要)
  - mcdreforged
- 一个公网 IP 或域名，能解析到你的服务器

## 选择 Java

根据 Minecraft 版本的不同，所需要的 Java 版本也不同，报错 `找不到主类 @user_jvm_args.txt` 的腐竹大概率是卡在这里了。下面列出几个常用的~~笔者搞过的~~版本对应关系：

- 如果你的服务器是 1.12.2 以及以下，请使用 Java 8
- 如果你的服务器是 1.16.5，请使用 Java 11
- 如果你的服务器是 1.17 以及以上，请使用 Java 17

这里推荐下 Azul Zulu 的 Java 运行时版本，参见 [更换 JVM](#更换 JVM)

## 配置 Forge 服务端

### 1. 下载

在 [Forge 官网](https://files.minecraftforge.net/net/minecraftforge/forge/) 上下载需要的版本的 Installer。
![Forge Download](https://img-blog.csdnimg.cn/698265827a694807b9d89b8dc8fa4159.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARlZvcnRleA==,size_20,color_FFFFFF,t_70,g_se,x_16)

点击 Forge 官网上的下载链接之后，会自动跳转到 AdFoc.us 这个网站，等待 5 秒点右上角 “SKIP” 按钮即可下载。

#### 救命！我下载不下来！AdFocus 网页打不开 / 没有 SKIP 按钮！

回到 Forge 官网上你需求的版本的下载页面，右键 Installer 按钮，复制链接并粘贴网页栏里面，把 `&url=` 以及前面的所有字符都去掉，再回车访问页面即可开始下载。

### 2. 安装服务端

![Forge Installer GUI](https://img-blog.csdnimg.cn/ccf8f1e7b1c344139aeb6a84e7acc475.png)

打开下载好的 Installer，选择 Install Server 选项，在右下角的三个点按钮那里选择你要安装到的文件夹，然后点击确定，即可开始安装。安装时需要联网下载相关库文件。
如果中途失败，可以接着失败前安装的进度重新跑一遍此流程，这里关于“非空文件夹”的红字提示可以忽略。
出现下面的提示时，安装成功。
![Forge Install Succeed](https://img-blog.csdnimg.cn/20185f89d3854b328f7f4afc2edd6288.png)
**自某个新版本起，forge 不再在服务器根目录下放 jar 文件，而是放了启动脚本。** 目前博主没有调查过到底是哪个版本起 forge 做出的这个改变，只知道 1.18.2-forge 40.0.36 是这样。

### 撰写启动脚本

#### 旧版 Forge（请原谅我用这种表述）

![在这里插入图片描述](https://img-blog.csdnimg.cn/fd4dbbbf9f724e4b8682d7eb7507ea95.png)

这个适用于 Forge 在服务器根目录下放置了两个 jar 文件的情况。

1. 新建一个文本文件，随意命名并修改后缀名使其成为可执行脚本（Linux 改为 `.sh`, Windows 改为 `.cmd`）
2. 用你喜欢的文本编辑器打开这个文件，输入

```bash
java -xms1024M -xmx2048M -jar forge-1.12.2-14.23.5.2860.jar nogui
```

这里解释一下 `java` 命令的各项参数：

- `java`：Java 可执行文件，在有多个不同版本 Java 时用你需要的版本的 Java 可执行文件的绝对路径代替
- `-xms<内存容量>`：最小分配给服务器的内存，格式为数字后加单位，例如 `-xms1024M` 是分配 1024 MB，`-xms16G` 是分配 16GB
- `-xmx<内存容量>`：最大分配给服务器的内存，格式同上，必须大于等于 `-xms` 参数给定的内存容量，同时小于服务器内存容量
- `-jar <JAR 文件路径>`：选择你命令 Java 启动的服务器 Jar 文件的路径；这里有两个 Jar 文件，名为 `minecraft_server.1.xx.x.jar` 的是原版服务器的 Jar 文件，另一个是我们想要的 forge 服务器文件。
- `nogui`：这是个传递给 forge 服务器的启动参数，代表了我们不需要服务器打开服务器 GUI。一般情况下这个 GUI 除了可以看 Java 内部内存占用情况以外一无是处，还会卡服。想看内存占用的话，建议用 Spark 模组（[MCMod 链接](https://www.mcmod.cn/class/4073.html)，[Curseforge 链接](https://www.curseforge.com/minecraft/mc-mods/spark)）代替。

我们用到的 java 命令的命令格式：

```bash
java [JVM 参数...] -jar <Jar 文件路径> [Jar 启动参数]
```

参数之间用空格隔开，遇到参数中带空格时用英文双引号将参数包起来，字符串中遇到反斜杠 `\` 的请使用 `\\` 进行转义。

#### 新版 Forge（请原谅我用这种表述）

新版的 Forge 将服务器 jar 包放在 libraries 文件夹里面，并通过自带的启动脚本进行启动。好处是不再需要命令行的知识便可以启动服务器了。需要配置服务器启动参数的时候只要修改脚本调用的参数文件即可。

##### 启动脚本

举例：mc1.18.2 - forge 40.1.60
`run.bat`:

```batch
REM Forge requires a configured set of both JVM and program arguments.
REM Add custom JVM arguments to the user_jvm_args.txt
REM Add custom program arguments {such as nogui} to this file in the next line before the %* or
REM  pass them to this script directly
java @user_jvm_args.txt @libraries/net/minecraftforge/forge/1.18.2-40.1.60/win_args.txt %*
pause
```

这里 `@user_jvm_args.txt` 代表这里会将 `user_jvm_args.txt` 文件中的 JVM 参数传入进来。后面的 `@libraries/net/minecraftforge/forge/1.18.2-40.1.60/win_args.txt` 则是调用 forge 内置的服务器启动参数，**相当于旧版的 `-jar <服务器JAR包>`**，一般情况下不需要修改这里。后面的 `%*`（在 `run.sh` 中是 `$*`）是用来接受命令行参数的，需要添加 `nogui` 的话，请在 `%*`（或 `$*`）前面添加。

后面的 `pause` 用于在服务器停机之后暂停处理，以维持 cmd 窗口，不是必须的，建议去掉。

**所以，如果需要设置内存大小、修改 JVM 参数，需要在同目录下的 `user_jvm_args.txt` 进行添加。**
 `user_jvm_args.txt` 中可以用 `#` 进行注释，参数的写法和正常命令行中的写法一致，不同之处在于文件中的换行会被视作参数间的分隔符。

### 首次启动服务器

首次启动服务器时几乎必定失败，因为这时服务端会生成一个 [EULA](https://baike.baidu.com/item/%E6%9C%80%E7%BB%88%E7%94%A8%E6%88%B7%E8%AE%B8%E5%8F%AF%E5%8D%8F%E8%AE%AE/7228616?fromtitle=EULA&fromid=10688321&fr=aladdin) 文件，默认为你不接受这个 EULA。而只有接受这个 EULA，服务器才可以运行。这时，打开服务器根目录下生成的 `eula.txt`，将 `eula=false` 改为 `eula=true` 并重启服务器即可。

#### server.properties

[server.properties](https://server.properties/) 是您服务器的一个基础的配置文件，具体各项信息请参照 [Minecraft Wiki](https://minecraft.fandom.com/zh/wiki/Server.properties)。这里仅说几项较为重要的。

##### server-port

这个是服务器的端口号，玩家使用 IP 地址或者域名进入服务器时会默认使用 25565 端口进服

##### server-ip

请留空代表本地服务器

##### query.port

这个代表用来访问服务器基本信息的端口号，一般与 server-port 一致

##### view-distance

这个决定了一个玩家所加载的区域大小，越大服务器需要的内存越多

### 服务器 Mod 配置

一些纯客户端 mod 是不可以安装到服务端的，如果安装上会导致服务器无法启动，例如 Optifine。
这些 mod 的共同特点：

- 会在服务端启动时报错: `java.lang.NoClassDefFoundError`, 而且找不到的类的路径中通常带有 `client` 或 `render`
- 一般会在 mod 介绍页面标明 client only
- 仅包含 GUI、资源包、渲染上的功能

### 其他服务端

为了提供更好的服务器游玩体验，服务器往往需要添加一些插件，但这些插件往往基于一些没有原生 forge 兼容的 API，例如 Bukkit 和 Spigot。这时就有一些大佬开发了“混合端”，用来兼容 Forge 和其他 API。这里仅列出一些知名服务端，具体使用方法还需后续补充 / 自行查询。注意，这些服务端对 Java 的要求可能和纯 Forge 的不一样，以混合端官方文档和答疑为准。

- 1.12.2
  - CatServer
  - Mohist
- 1.16.5
  - LoliServer
- 1.14.4+
  - [Arclight](https://www.mcmod.cn/class/3060.html)

## 服务器配套设施

配套设施都不是必须的，但可以提升服务器体验以及增加服务器安全系数。

### MCDR

[官方文档](https://mcdreforged-docs-zh-cn.rtfd.io) 已经很详细了，这里不再赘述。
MCDR 官方 QQ 群：1101314858

#### MCDR 插件

插件库：[https://github.com/MCDReforged/PluginCatalogue](https://github.com/MCDReforged/PluginCatalogue)

推荐插件（安装使用方法看插件文档）：

- ChatBridge：用于服务器和 QQ群、~~开黑啦~~ Kook 服务器进行聊天互通
  - 不支持 Python 3.10 （2022 Apr.2）
- Auto Plugin Reloader：用于自动重载插件配置文件
- Timed QBM：用于自动定时备份服务器

### 外置登录和皮肤站

#### 搭建皮肤站

请看 [教程](https://www.mintimate.cn/2021/09/26/MinecraftBlessing/)

#### 配置外置登录

1. 下载 authlib injector 并放到服务器根目录。[下载链接](https://authlib-injector.yushi.moe/)
2. 在服务器启动脚本中的 JVM 参数里面，增加参数 `-javaagent:"<authlib injector 路径>"=<认证服务器 URL>` 即可
3. 将服务器根目录中的 `server.properties` 中的 `online-mode` 改为 `true`

### SpongeForge

这里并不推荐在 1.12.2 使用 SpongeForge，在与某些 mod 的共同作用下可能有内存泄露的问题，容易卡服 / 崩服。

#### 获取和安装 SpongeForge

[官网](https://www.spongepowered.org/downloads/spongeforge) 上可以选择对应版本并下载，下载后直接拖进服务器 `mods` 文件夹即可。

#### 配置 SpongeForge

[官方中文文档](https://docs.spongepowered.org/stable/zh-CN/server/index.html)
**看文档时记得在左下角选择服务器所使用的 Sponge 版本**

#### Sponge 插件

Sponge 插件的来源有很多，有 Sponge 官方插件库 [Ore](https://ore.spongepowered.org/)，有 [MCBBS 上的插件板块](https://www.mcbbs.net/forum.php?mod=forumdisplay&fid=138&filter=sortid&sortid=7)，找别人定制插件，或者自己写插件。
自己写插件时请参考 [Sponge 官方插件开发文档](https://docs.spongepowered.org/stable/zh-CN/plugin/index.html)。
**看文档时记得在左下角选择服务器所使用的 Sponge 版本**

### Bungeecord & Velocity

这是两个用于组建群组服务器的工具，目前还没搞懂，等笔者后续填坑。

## 软硬件资源获取

### 公网 IP 或域名

公网 IP 在国内是非常稀缺的资源，不容易弄到，一般可以从阿里云或腾讯云租赁到一个服务器，这个服务器的地址就可以作为连接到服务器的地址。但租一个性能强劲的服务器是很贵的，这时候就可以用 FRP 将你的服务器中转到公网上。具体使用方法请参照官方文档。

[FRP 官方简中 Readme](https://github.com/fatedier/frp/blob/dev/README_zh.md)
[FRP 官网简中文档](https://gofrp.org/docs/)
[FRP GitHub Repo](https://github.com/fatedier/frp)

## 服务器性能优化

服务器设置好之后，有可能因为各种各样的原因出现卡顿。卡顿主要的来源有：

- CPU 单核性能太差
- 内存不足导致频繁 GC
- 玩家骚操作，例如超音速飞行跑图导致大量生成区块
- 同时加载的实体太多

为了给玩家更好的体验，我们需要对服务器进行调优。

### 卡顿检测

推荐使用 Spark 这个模组/插件来检测服务器目前运行时的占用。
[MCBBS 介绍](https://www.mcbbs.net/forum.php?mod=viewthread&tid=823209)
[GitHub Repo](https://github.com/lucko/spark)
[官网](https://spark.lucko.me/)

### 调整游戏设置

其中有一些可以通过调整游戏内设置解决，例如禁止飞行，高速移动检测，缩小视距等。

### 增加优化 Mod

- 直接的性能优化
  - Surge
  - FoamFix
  - Phospher
- 限制实体生成：In Control
- 根据 mspt 动态调整视距：Dynview
- 合并经验球：Clumps

### JVM

#### 调整 JVM 参数

[Aikar: 调整JVM —— 非常有效的服务器启动参数 - 联机教程 - Minecraft(我的世界)中文论坛 -](https://www.mcbbs.net/thread-867786-1-1.html)

<a id="更换 JVM"></a>

#### 更换 JVM

Oracle 官方的 JVM Hotspot 在性能方面其实是一坨 sh*t，有在 CPU 占用、内存占用和 GC 上全方位超越他的存在。这里请参看 [MCBBS 上对 JVM 性能的测试](https://www.mcbbs.net/forum.php?mod=viewthread&tid=1232993)。笔者在这里推荐 Azul Zulu [下载页面](https://www.azul.com/downloads/)。
