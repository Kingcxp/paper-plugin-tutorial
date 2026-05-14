---
marp: true
theme: gaia
size: 16:9
math: katex
backgroundColor: #141414
color: #E5EAF3
style: |
  p {
    margin: 16px;
  }

  section {
    position: relative;
    font-family: Bahnschrift;
    padding-bottom: 64px !important;
    padding-top: 64px !important;
  }

  section.title-page {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  section::after {
    content: "";
    background-color: #1D1E1F;
    margin-top: auto;
    height: 64px;
    width: 100%;

    display: flex;
    flex-direction: row-reverse;
    z-index: 0;
  }

  header {
    z-index: 1;
    color: #CFD3DC;
  }

  footer {
    z-index: 1;
    color: #CFD3DC;
  }

  img {
    max-height: 50vh;
    max-width: 100%;
  }

  strong {
    color: #409EFF !important;
  }

  success {
    color: #67C23A !important;
  }

  warning {
    color: #E6A23C !important;
  }

  danger {
    color: #F56C6C !important;
  }

  info {
    color: #909399 !important;
  }

  code {
    background-color: #0A0A0A;
  }

  pre {
    background-color: #0A0A0A;
  }
---

<!-- _class: title-page -->

# Minecraft Paper 服务器和插件开发
### By Kingcq

---

### Paper 服务端是从哪来的？

![](./assets/minecraft-plugin-history.png)

- <info>Vanilla</info>：Mojang 官方原版，无法加载插件。
- **CraftBukkit**：第一个广泛使用的插件支持
- <success>Spigot</success>：CraftBukkit 的一个分支，提供了更多的 API 和性能优化。
- <danger>Paper</danger>：Spigot 的一个分支，在 API 支持和性能上更进一步。

---

### **插件**相较于<danger>模组</danger>和<success>数据包</success>？

- ##### 为什么选择**插件**？

插件结合了两者对 Minecraft 游戏世界的修改能力，同时将两者拥有的一些缺陷进行了**弥补和隐藏**：

我们从三个角度分别来看一下：

- **编写难度**：是否能快速上手并编写出功能？
- **安装难度**：完成的作品在玩家安装时是否困难？
- **使用效果**：是否能够实现足够多样的功能？

---

### 📁 数据包 (Datapack)

- **编写难度**：<warning>入门极易，精通极其折磨</warning>
  基于 JSON 和原版命令 (mcfunction)。修改合成表、战利品极快。但若要实现复杂的逻辑（如循环、复杂的数学运算），只能依靠记分板和实体，逻辑链路极难维护。
- **安装难度**：<success>完全零门槛（玩家无感）</success>
  纯粹依托于游戏存档/服务端。玩家使用完全纯净的原版客户端即可加入，无需任何额外操作。
- **使用效果**：<danger>局限性强，性能上限低</danger>
  被死死限制在原版命令允许的框架内。无法连接数据库，无法实现多线程处理，高频复杂的命令运行会严重拖垮服务器计算资源 (TPS)。

---

### 🧩 模组 (Mod - Forge/Fabric/NeoForge)

- **编写难度**：<danger>地狱级 (极陡峭的学习曲线)</danger>
  需要深厚的 Java 功底，掌握 Mixin 字节码注入，并且需要大致理解晦涩的 Minecraft 底层源码（NMS）。
- **安装难度**：<danger>门槛较高</danger>
  玩家必须在自己的电脑上安装对应的加载器，并将几十上百个 Mod 文件精确放入客户端文件夹，版本稍微不匹配就会崩溃报错。当然也可以选择整合包一键安装，但这仍然需要一定的操作门槛。
- **使用效果**：<strong>无所不能，没有边界</strong>
  客户端和服务端同时运行。可以彻底重写光影渲染引擎、加入全新的维度世界、全新的按键绑定、完全自定义的 GUI 界面系统。

---

### 🔌 服务器插件 (Plugin)

- **编写难度**：<info>中等偏上 (需要 Java 基础与 API 知识)</info>
  基于高度封装的 Paper/Spigot API，拥有现代编程语言完整的面向对象体验和庞大的开源生态，遇到问题很容易查到现成代码。
- **安装难度**：<success>完全零门槛（玩家无感）</success>
  纯服务端运行，玩家依然使用原版客户端直连。除了需要在服务端安装对应插件以外，无需任何额外操作。这只会对腐竹产生一些麻烦。
- **使用效果**：<success>逻辑控制极强</success>
  可以连接 Redis/MySQL，彻底掌控服务器逻辑。唯一遗憾是 **不能“无中生有”**：无法给客户端注入全新的非原版 3D 渲染模型、按键绑定或全新机制的方块。

---

### 💪 为什么要选择服务器插件？

- **开发难度适中**：不需要掌握复杂的底层技术，只需要掌握 Java 编程语言和 Paper/Spigot API 即可，而 API 这块根本不必担心，因为有详尽的[文档](https://jd.papermc.io/paper/26.1.2/)。
- **用户友好**：玩家根本就不需要操心插件的事情，只要是原版客户端，就能无缝加入服务器。~~*只是需要可怜一下腐竹*~~
- **功能强大**：相较于同样方便的数据包，插件显然有更多的可能性，搭载了 Java 编程体系使得它能够使用所有 Java 生态中的工具和库，能够实现数据持久化、多线程处理、连接数据库、实现复杂的逻辑等等。

---

### 🌏 玩家 & 世界

在正式认识插件之前，我们要先来理解玩家如何与游戏世界交互：

![](./assets/mindmap.png)

---

### 🌍 实体 & 世界

除了玩家的操作，**实体与实体**、**方块与环境** 每时每刻都在发生着不依赖于玩家的交互。这些交互同样会被服务器捕获，并广播为事件。

想象以下场景：
1. **僵尸看到了村民**并开始追逐。
2. **闪电**击中了一只猪。
3. 悬空的**沙子**受重力影响开始下落。
4. 漏斗将箱子里的**物品吸走**。

---

### 📜 监听万物：Bukkit 事件

这些事件明显可以监听并触发一些额外的逻辑，来实现一些特殊功能

![](./assets/events.png)

---

### ⏱️ 世界的脉搏：Tick (游戏刻)

Tick 是 Minecraft 游戏世界的“心跳”，每秒执行 20 次。

在一个 tick 中，服务器会处理所有的事件，并更新整个世界的状态：

- <success>处理玩家行为</success>
- <success>实体与 AI 演算</success>
- <success>世界环境更新</success>
- <success>服务器定时任务</success>

如果在这个 <danger>50ms</danger> 的时间窗口内不能完成所有任务，那么服务器就会卡住，表现为 **TPS 下降，玩家操作卡顿**。

---

<!-- _class: title-page -->

# Paper 插件开发 - 创建插件

---

### 📦 安装开发环境

要想顺利开发 Paper 插件，我们必须要一个合适的开发环境：

- 我们先要一个[足够强力的 IDE](https://lp.jetbrains.com/intellij-idea-promo/?msclkid=fcc138a0ef46179150cb52a5398cad60&utm_source=bing&utm_medium=cpc&utm_campaign=APAC_en_JP_IDEA_Branded&utm_term=intellij%20IDEA&utm_content=intellij%20idea)（IntelliJ IDEA）
- 还要一个有力气的 IDE 插件：[**Minecraft Development**](https://plugins.jetbrains.com/plugin/8327-minecraft-development)

![](./assets/minecraft-development.png)

---

### 🍵 创建插件项目

- 选择新建项目，在生成器中找到 Minecraft，填写必要的信息后就可以成功创建一个 Paper 服务器插件项目了，有两个要注意的点：
  - [Java 包名规范](https://www.cnblogs.com/JavaWebStudyhcz/articles/18969795)
  - [使用魔法](https://ikuuu.win)

其它的注意点不用考虑，因为 IDEA 和 Minecraft Development 插件已经帮你做好了所有的考量

---

<!-- _class: title-page -->

# Paper 插件开发 - 插件和生命周期

---

### 🔌 插件主类

当插件项目自动创建完之后，我们就会看到对应的初始代码：

```java
public final class TestPlugin extends JavaPlugin {

    @Override
    public void onEnable() {
        // Plugin startup logic
    }

    @Override
    public void onDisable() {
        // Plugin shutdown logic
    }
}
```
这是插件的核心，所有的插件逻辑都会围绕这个类展开。

---

### 📜 插件的生命周期

插件的生命周期准确说分为三个阶段，而第一个阶段一半很少用到：

- `onLoad()` - 在插件被读取，但还未被加载到服务器时调用
- `onEnable()` - 在插件被加载到服务器时调用
- `onDisable()` - 在插件被卸载时调用

非常朴实无华的生命周期，但却能够包含整个插件的运作过程。
