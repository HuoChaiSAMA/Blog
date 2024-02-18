---
abbrlink: MC_Server
categories:
- - Minecraft
date: '2023-06-22T09:04:20.678291+08:00'
description: H.C.Server 开始启动！
headimg: https://www.hcspace.top/images/background11.webp
keywords: Minecraft
readmore: true
tags:
- Web
- Game
title: Minecraft Server
updated: 2023-10-1T17:24:18.173+8:0
---
# Henry's Minecraft Server!!!

**H.C.Server** 开始启动！

---

# 服务器介绍

**H.C.Server** 的云端部署！

## 故事

**HuoChaiSAMA** 一直有一个 **Minecraft** 小服务器，但一直都是本地运行，然后拿 **Sakura Frp** 做了个内外穿透。

本地服务器的好处就是**不怎么花钱** ~~（**花了也看不到**）~~，但缺点很明显：服务端和客户端**都在本地**，**占内存多**，而且服务器运行**必须腐竹开电脑**，也就是说，这就相当于**一个联机房间**，只是**用服务端跑起来的**。

又到了暑假，这次 **HuoChaiSAMA** 想试一试云服务器，实现真正的服务器功能

于是，他找到了 [PixelCloud - 简单好用的 Minecraft 服务器提供商 我的世界国际服租赁开服](https://pixelcloud.cn/)

## 服务器简介

### 服务器信息

* 腐竹：`HuoChaiSAMA`
* 名称：`H.C.Server`
* 服务器版本：`Minecraft Java Edition 1.20.1`
* 客户端版本：`Minecraft Java Edition 1.7.10 ~ 1.20.X` （使用`ViaVersion`进行版本兼容，但低版本不会显示高版本物品等）
* 服务器定位：生存、生电、娱乐
* 服务器官方客户端：[1.20 FUNFUNFUN.zip | Henry's Drive (hcspace.top)](https://pan.hcspace.top/Minecraft/%E6%95%B4%E5%90%88%E5%8C%85/1.20%20FUNFUNFUN.zip)
* 服务器ip：`mc.hcspace.top` 或 `hcgame.playmc.fun`
* 服务器白名单：通过管理员获取
* 服务器核心类型：`Spigot`

### 服务器插件

* [Simple Voice Chat - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/simple-voice-chat)
* [Chunky - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/chunky)
* [CoreProtect - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/coreprotect)
* [NoCheatPlus/NoCheatPlus: Anti cheating plugin for Minecraft (Bukkit/Spigot). (github.com)](https://github.com/NoCheatPlus/NoCheatPlus)
* [[管理|开源]QiQWhiteList —— 支持连接MySQL的白名单[1.8-1.18] - 服务端插件 - Minecraft(我的世界)中文论坛 - (mcbbs.net)](https://www.mcbbs.net/forum.php?mod=viewthread&tid=1182399)
* [July7th/QiQWhiteList (github.com)](https://github.com/July7th/QiQWhiteList)
* [SkinsRestorer | SpigotMC - High Performance Minecraft](https://www.spigotmc.org/resources/skinsrestorer.2124/)
* [UserLogin | SpigotMC - High Performance Minecraft](https://www.spigotmc.org/resources/userlogin.80669/)
* [ViaVersion | SpigotMC - High Performance Minecraft](https://www.spigotmc.org/resources/viaversion.19254/)
* [Simple Voice Chat - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/simple-voice-chat)
* [Let Me Despawn - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/lmd)

### 服务器物理配置

* 4核 8GB
* e7-4870 2.6 GHz

### 服务器预计运行时间

2023/6/22 - 2023/8/20

---

## 服务器群组

使用**KOOK服务器**

邀请链接： [KOOK (kookapp.cn)](https://www.kookapp.cn/app/invite/DbpsA9)

# 其他

## 服务器语音模式

### 1. Simple Voice Chat 游戏内置语音

Mod下载：[Simple Voice Chat - Minecraft Plugin (modrinth.com)](https://modrinth.com/plugin/simple-voice-chat)

安装好后，在游戏内用`V`键打开菜单，游戏中按住`Capslock`说话。支持群组。

### 2. KOOK 游戏外语音

在KOOK群组中获得`服务器成员`用户组后，可在语音聊天室进行语音聊天

---

## 服务器配置文件

```yml
#Minecraft server properties
#Thu Jun 22 09:01:15 CST 2023
enable-jmx-monitoring=false
rcon.port=25575
level-seed=
gamemode=survival
enable-command-block=false
uuid=dc5fd052-6363-49ee-88ac-975bfa0e6621
enable-query=true
generator-settings={}
enforce-secure-profile=true
level-name=world
motd=§6Henry's§a Minecraft Server§r\n§e已开启§4白名单§e，请在管理员处获取§r.§dWELCOME\!\!\!
query.port=25565
pvp=true
generate-structures=true
max-chained-neighbor-updates=1000000
difficulty=hard
network-compression-threshold=256
max-tick-time=60000
require-resource-pack=false
use-native-transport=true
max-players=114514
online-mode=false
enable-status=true
allow-flight=false
initial-disabled-packs=
broadcast-rcon-to-ops=true
view-distance=10
server-ip=
resource-pack-prompt=
allow-nether=true
server-port=25565
enable-rcon=false
sync-chunk-writes=true
op-permission-level=4
prevent-proxy-connections=false
hide-online-players=false
resource-pack=
entity-broadcast-range-percentage=100
simulation-distance=10
rcon.password=
player-idle-timeout=0
debug=false
force-gamemode=false
rate-limit=0
hardcore=false
white-list=false
broadcast-console-to-ops=true
spawn-npcs=true
previews-chat=false
spawn-animals=true
function-permission-level=2
initial-enabled-packs=vanilla
level-type=minecraftserver-portv6\=25566
text-filtering-config=
spawn-monsters=true
enforce-whitelist=false
spawn-protection=16
resource-pack-sha1=
max-world-size=29999984
```
