<p align="center" style="margin-bottom: 4px;">
  <img src="docs/controls/SNESTICLE.png" alt="SNESTICLE" width="500">
</p>

<p align="center" style="margin-top: 0;">
  <b><font size="7">
    SNESticle 复活版 PlayStation 2<br>
    自制程序与 S/NES 模拟器！
  </font></b>
</p>

**SNESticle** 的复活且积极维护的源代码，这是一个传闻已久的
**超级任天堂（SNES）模拟器**，由 **Icer Addis (iaddis)** 编写。

SNESticle 曾著名地隐藏在 EA 的 **《拳击之夜 2》（Fight Night Round 2，2005）** 的
**GameCube** 版本中，在其中运行了 **《超级拳无虚发》**。社区在
**2022 年** 对该版本进行了逆向工程并提取出来，Sardu 以 **MIT 许可证** 发布了源代码。此仓库让这份代码
得以延续：重组为逻辑目录、修复、扩展，并且如今易于构建和研究。

在 SNES 核心之上，该项目现在还集成了 **InfoNES**，将
**NES** 模拟带到了 **PlayStation 2** 上。

---

## 📚 目录

- [⚠️ 注意事项](#️-注意事项)
- [🚀 功能特性](#-功能特性)
- [🎮 控制](#-控制)
- [🖼 封面艺术](#-封面艺术)
- [🎵 菜单音乐与音频](#-菜单音乐与音频)
- [💾 存储与设备](#-存储与设备)
- [🔨 构建](#-构建-playstation-2)
- [📝 近期完成的工作](#-近期完成的工作)
- [🐞 已知问题](#-已知问题--仍缺失)
- [📂 项目布局](#-项目布局)
- [❤️ 致谢](#-致谢)
- [📜 许可证](#-许可证)

---

## ⚠️ 注意事项

> [!WARNING]
> **注意**
>
> **主要目标：** **PlayStation 2**（EE/IOP，gsKit）。开发基于 PS2SDK 进行，该附加组件可在所有支持 PS2SDK 的设备上运行。
>
> 此版本无需创建 ISO；仅当您想向社区分发时才需要这么做。
>
> **请不要从自制程序中移除 Icer Addis (iaddis)（SNESticle 的原始创建者）或我（ReyFxck，SNESticle Revive 的维护者）的致谢信息。**
---
## 🚀 功能特性
<details>
<summary>🕹️ SNES 进度</summary>

**系统**
- **SNES** — 原始的 SNESticle 核心（65816 汇编 CPU、SPC700、PPU）。
- **NES** — 通过 **InfoNES**（`src/nes/`），音频接入 PS2 音频路径。

**SNES 特殊芯片（协处理器）：**
- **DSP‑1 / DSP‑1B** — 飞行员之翼、超级马里奥赛车等。（`sndsp1`）— 净室实现
- **DSP‑2** — 地下城主（`sndsp2`）— 净室实现
- **DSP‑4** — 顶级赛车 3000（`sndsp4` + `dsp4emu`），**HLE / 独立**（无
  外部文件）。完整的赛道投影数学来自 **ZSNES** DSP‑4 HLE
  （GPLv2，© ZSNES 团队），已移植并注明出处——这也是此分支
  现在采用 **GPLv2** 的原因（见 [许可证](#license)）。
- **CX4** — 洛克人 X2 / X3（`sncx4`）
- **OBC1** — 金属战斗（`snobc1`）
- **S‑DD1** — 星之海洋、街头霸王 Alpha 2（`snsdd1`）
- **S‑RTC** — 大怪兽物语 II（`snsrtc`）
</details>
<details>
<summary>🎮 PS2 进度</summary>

**PlayStation 2 平台**
- 基于 gsKit 的视频后端，带有 **视频配置** 屏幕。
- 两种隔行视频模式：**480i**（默认，普遍兼容）和
  **1080i**，带有居中的 4:3 视口，以及屏幕偏移、过扫描和
  宽屏。以前的 240p/288p 和 480p 路径已移除，以保持 GS
  设置稳定在 640x480 隔行帧缓冲路径上。
- 可切换的 SNES 颜色配置：**原始**（默认）以及模拟器
  恢复的 **复合** YIQ 校准；可以实时预览选择。
- ROM 浏览器中的 **封面艺术** —— 包装盒艺术、标题画面、游戏截图、
  标志以及来自 PNG 文件的手动附加内容，由 **upng** 解码。Libretro 艺术
  可以通过 `COVER=y` 自动获取。见 [封面艺术](#-封面艺术)。
- ROM 浏览器直接使用每个驱动的目录记录，避免了
  对每个 CDFS 条目单独进行 `stat`/光盘寻道，并动态增长列表。
  目录类型以 iomanX 返回的标准化 `FIO_S_*` 格式读取，
  仅对返回未知类型的驱动进行完整路径探测。
  其嵌入式流式 CDFS 驱动移除了 PS2SDK 固定的 256 条目 ISO
  表。文件夹显示为 `> NAME/`；标记和末尾斜杠保持
  可见，只有很长的中间名称会被截断或滚动。
- 启动普通、ZIP 或 GZ ROM 使用批量 `fileXio` 读取，而不是小的
  stdio RPC。加载器不再每次打开 ROM 两次，也不会在解析前清除
  一个未使用的 8 MiB 缓冲区，从而降低了慢速设备上的启动延迟。
- **菜单音乐** —— 追踪器音乐（`.mod` / `.xm`）在 ROM 浏览器和
  暂停菜单中播放，带有音量和合成速率控制。见
  [菜单音乐与音频](#菜单音乐与音频)。
- 通过 **audsrv** 播放音频，在视频配置屏幕中有单独的 **游戏音量** 和 **菜单音乐**
  控制。其停止/恢复状态是显式的，因此
  菜单和 SNES 音频不再依赖于启动后打开 NES 游戏。
- **SNES 和 NES 卡带即时存档** —— 五个存档位；USB、记忆卡、MMCE
  和内置 HDD 存储；ROM 和 CRC 校验；以及双银行写入，
  保留之前有效的状态。
- **电池 SRAM** —— SNES 和 NES 存档分别存放于
  `mc0:/SNESticle/SNES/` 和 `mc0:/SNESticle/NES/`；v1.0.3 根布局中的旧 SNES 存档会被加载并迁移，而不会删除原始文件。
- 控制器 / 记忆卡 / IRX 初始化流程与 **Open‑PS2‑Loader** 风格保持一致。
- **存储**：USB（×2）、外置 HDD/SSD 和 **MX4SIO** SD 卡作为
  `mass0:`/`mass1:`；内置 **HDD**（`hdd0:`）；记忆卡
  （`mc0:`/`mc1:`），包括 **MMCE** 卡带（MemCard PRO 2 / SD2PSX）作为
  `mmce0:`/`mmce1:`。通过捆绑的 BDM 栈读取 FAT16/FAT32/**exFAT**，支持 MBR/GPT 分区
  表。见 [存储与设备](#存储与设备)。
- 网络对战代码（`src/modules/netplay/`）。
</details>

---

## 🎮 控制

PS2 手柄映射到 SNES 控制器。**L2 + R2**（同时按下）可随时在
游戏和菜单之间切换，打开菜单时会刷新更改过的 SRAM。菜单首先显示；待处理的 SRAM 写入在两个渲染帧后开始，并在完成时报告，不再有旧的固定一秒钟暂停。

<details>
<summary>🎮 游戏中</summary>

**游戏中**

| 按钮 | SNES |
|:------:|------|
| <img src="docs/controls/dpad.svg" height="20" alt="D-Pad"> | 方向键 |
| <img src="docs/controls/cross.svg" height="20" alt="Cross"> | B |
| <img src="docs/controls/circle.svg" height="20" alt="Circle"> | A |
| <img src="docs/controls/square.svg" height="20" alt="Square"> | Y |
| <img src="docs/controls/triangle.svg" height="20" alt="Triangle"> | X |
| <img src="docs/controls/l1.svg" height="20" alt="L1"> / <img src="docs/controls/r1.svg" height="20" alt="R1"> | L / R |
| <img src="docs/controls/select.svg" height="20" alt="Select"> | Select |
| <img src="docs/controls/start.svg" height="20" alt="Start"> | Start |
| <img src="docs/controls/l2.svg" height="20" alt="L2"> + <img src="docs/controls/cross.svg" height="20" alt="Cross"> | 保存状态到当前存档位 |
| <img src="docs/controls/l2.svg" height="20" alt="L2"> + <img src="docs/controls/circle.svg" height="20" alt="Circle"> | 从当前存档位加载状态 |
| <img src="docs/controls/l2.svg" height="20" alt="L2"> + <img src="docs/controls/r2.svg" height="20" alt="R2"> | 打开菜单并刷新更改的 SRAM |

</details>

<details>
<summary>📂 菜单与 ROM 浏览器</summary>

**菜单与 ROM 浏览器**

| 按钮 | 操作 |
|:------:|--------|
| <img src="docs/controls/dpad.svg" height="20" alt="D-Pad"> 上 / 下 | 移动选择 |
| <img src="docs/controls/cross.svg" height="20" alt="Cross"> 或 <img src="docs/controls/start.svg" height="20" alt="Start"> | 启动高亮的 ROM（或打开文件夹） |
| <img src="docs/controls/triangle.svg" height="20" alt="Triangle"> | 返回上一级文件夹（`..`） |
| <img src="docs/controls/square.svg" height="20" alt="Square"> | 上一页 —— *或者当封面艺术开启时切换封面图像（见下文）* |
| <img src="docs/controls/circle.svg" height="20" alt="Circle"> | 下一页 |
| <img src="docs/controls/select.svg" height="20" alt="Select"> | 文件菜单（复制 / 粘贴 / 删除） |
| <img src="docs/controls/l1.svg" height="20" alt="L1"> / <img src="docs/controls/r1.svg" height="20" alt="R1"> | 切换屏幕（浏览器 ⇆ 状态管理器 ⇆ 网络 ⇆ 菜单 ⇆ 日志 ⇆ 视频配置），包括游戏暂停时。 |
| <img src="docs/controls/l2.svg" height="20" alt="L2"> + <img src="docs/controls/r2.svg" height="20" alt="R2"> | 返回游戏 |

</details>

<details>
<summary>💾 即时存档</summary>

**电池 SRAM**

卡带电池存档使用模拟器 PS2
记忆卡存档中的独立目录：`mc0:/SNESticle/SNES/<game>.srm` 和
`mc0:/SNESticle/NES/<game>.srm`。NES SRAM 仅对头部包含电池标志的 iNES ROM 启用，并存储完整的 8 KiB 卡带 RAM。
使用 **L2 + R2** 打开游戏内菜单会刷新更改的 SRAM。
UI 不再等待记忆卡后才进入菜单：写入
延迟两个可见帧，而菜单音乐 I/O 辅助程序在同步卡操作期间保持
已加载的曲目供给 `audsrv`。

为了向后兼容，`SNES/` 中缺失的 SNES 存档会在
旧的 `mc0:/SNESticle/<game>.srm` 位置查找。它会正常加载，并在下一次 SRAM 保存时将副本
写入 `SNES/`；旧文件永远不会被删除。

**首次即时存档目标**

首次在游戏中按下 **L2 + Cross** 会打开一个小的临时 **即时存档位置**
屏幕。选择 **自动**、**USB**、**记忆卡**、**MMCE** 或
**内置 HDD** 并按 Cross：目标会被记住，第一个状态被
保存，屏幕自动关闭返回游戏。按 Circle 取消。所有五个选项始终显示，因此可移动设备即使在设置时未插入，也可以被选为将来的默认目标。保存到
当前不可用的目标会报告正常错误，而不是静默地
更改偏好。

之后的 **L2 + Cross** 按下直接保存，**L2 + Circle** 直接加载。
这个临时选择器暂停时不会刷新 SRAM；**L2 + R2** 仍然是
专用的菜单/SRAM 快捷键。选择存储在 `state.cfg` 中。现有的
`mc0:/SNESticle/state.cfg` 和 `mc1:` 文件保留优先级；如果没有可写的
卡，模拟器可以将其持久化在可写的独立 ELF 旁边，然后在
`mass0:`/`mass1:`/旧版 `mass:`（USB 或 MX4SIO）或
已启用的 MMCE 插槽上的 `SNESticle/state.cfg` 下。如果之后从光盘/ISO 启动并打开另一个本地单元（如 `mass2:` 或 HDD 分区）中的 ROM，那么该 ROM 设备也会被使用，并且其选择会在 ROM 打开后立即恢复。CDFS 和 SMB 保持只读，永远不会被选为配置或状态写入目标。

**自动** 首先尝试提供 ROM 的设备，然后是可用的
`massN:`、`mc0:`/`mc1:` 和已启用的 `mmce0:`/`mmce1:` 设备。**USB** 涵盖
USB 闪存驱动器、外置 USB HDD/SSD 以及以 `massN:` 暴露的 MX4SIO 设备。
**记忆卡** 尝试两个 PS2 插槽。**MMCE** 在视频配置中启用 MMCE
支持时尝试两个 MMCE 插槽。**内置 HDD** 写入与当前 ROM 相同的已挂载
APA/PFS 分区。它可以随时被选为默认值，但
实际的 HDD 保存要求当前 ROM 是从
已启用的内置 HDD 分区打开的。自动始终使用快速存档位 1；如果
只有 PS2 记忆卡可用，它会回退到该卡，优先选择
`mc0:`。

如果选定的 PS2 记忆卡存在但未格式化，模拟器会询问
是否格式化。默认选中安全的 **否 / 取消** 选项，
警告明确说明格式化会擦除整张卡。当更改的 SRAM 需要未格式化的 `mc0:` 卡时，也会使用相同的确认。

**状态管理器**

常规的 L1/R1 标签页是一个文件管理器，可在初始自制程序
屏幕以及游戏暂停时使用：

| 选项 | 操作 |
|--------|--------|
| **浏览状态文件** | 打开所选存储设备的状态文件夹。单独的浏览器隐藏无关文件；按 Select 打开文件菜单并删除状态，或按 L1 返回。 |
| **存储** | 循环切换 `mass0:`、`mass1:`、旧版 `mass:` 别名、`mc0:`、`mc1:`、已启用的 MMCE 插槽和已启用的内置 HDD。 |
| **快速存档位** | 为显式设备选择快速存档位 1–5。自动保持在存档位 1。 |
| **再次询问保存位置** | 忘记当前目标，以便下一次 L2 + Cross 再次询问。 |

内置 HDD 管理首先打开其 APA 分区列表；进入所需
分区，然后进入 `SNESticle/states`。每个存档位有 `a` 和 `b` 两个银行；
SNES 使用 `.sNa`/`.sNb`，NES 使用 `.nNa`/`.nNb`。从状态浏览器删除任一匹配
文件会自动删除两个银行。在 PS2 记忆卡上，这些银行仍然直接位于 `mcN:/SNESticle/` 中，与之前一样；`SNES/`
和 `NES/` 子目录仅用于 SRAM。

每个存档位保留两个银行。新银行只有在完整
负载写入之后才会被提交，并且每次加载都会检查格式版本、ROM CRC、
ROM 大小和负载 CRC。如果最新的银行不完整或损坏，则会自动使用
较旧的有效银行。新银行使用快速 deflate 压缩，
并在成功写入后复用头部扫描而不重新读取负载，减少慢速设备 I/O；现有的未压缩版本 1 银行仍然可以加载。

即时存档覆盖 **基础 SNES 硬件** 和 **iNES 卡带游戏**。NES
状态保存 CPU、RAM、PPU、CHR RAM、pAPU、卡带 SRAM、活动银行
映射、Mapper RAM、Mapper 寄存器/锁存器和 IRQ 计数器；银行映射
存储为区域/偏移引用而不是进程指针，因此状态
在模拟器重启后仍然有效。不支持 FDS 状态。

使用 DSP、SuperFX、CX4、OBC1、S‑DD1、S‑RTC 或 Super Game Boy
硬件的 SNES 游戏仍会被拒绝，并显示明确消息，直到这些协处理器
状态被序列化。

</details>

<details>
<summary>⚙️ 视频配置</summary>

**视频配置屏幕**

| 按钮 | 操作 |
|:------:|--------|
| <img src="docs/controls/dpad.svg" height="20" alt="D-Pad"> 上 / 下 | 选择选项 |
| <img src="docs/controls/dpad.svg" height="20" alt="D-Pad"> 左 / 右 | 更改其值 |
| <img src="docs/controls/square.svg" height="20" alt="Square"> | 重置屏幕偏移 |
| <img src="docs/controls/cross.svg" height="20" alt="Cross"> 或 <img src="docs/controls/start.svg" height="20" alt="Start"> | 将设置保存到记忆卡 |

</details>

---

## 🖼️ 封面艺术

<details>
<summary>显示详情</summary>


ROM 浏览器可以在游戏列表旁边显示包装盒艺术、标题画面、游戏截图和
标志。启用 **视频配置 → 封面艺术**，然后按 ✕
保存设置。**一次只显示一张图像**；安装全部
四种类型可让它们循环切换，而不是同时绘制四张图像。

### 1. 最重要的规则：匹配 ROM 名称

PNG 文件名必须与 ROM 文件名匹配，仅移除其最终扩展名：

| ROM | 所需艺术作品文件名 |
|-----|---------------------------|
| `Super Mario World (USA).sfc` | `Super Mario World (USA).png` |
| `Super Mario World (USA).zip` | `Super Mario World (USA).png` |
| `Super Mario Bros. (World).nes` | `Super Mario Bros. (World).png` |

匹配不区分大小写，但名称的其他所有部分都很重要。例如，
手动安装的 `Super Mario World (USA).png` 不会匹配名为
`Super Mario World (U) [!].smc` 的 ROM。

该项目使用以下艺术作品收藏：

- [Libretro — 超级任天堂 / SNES](https://thumbnails.libretro.com/Nintendo%20-%20Super%20Nintendo%20Entertainment%20System/)
- [Libretro — 任天堂娱乐系统 / NES](https://thumbnails.libretro.com/Nintendo%20-%20Nintendo%20Entertainment%20System/)

每个收藏包含 SNESticle Revive 识别的四种艺术作品类型：

| 目录 | 艺术作品 |
|-----------|---------|
| `Named_Boxarts/` | 游戏包装盒或封面艺术 |
| `Named_Titles/` | 标题画面 |
| `Named_Snaps/` | 游戏内截图 |
| `Named_Logos/` | 透明游戏标志或图标 |

### 2. 两种受支持的布局

对于单张图像，将 PNG 放在 ROM 旁边：

```text
ROMS/
├── Super Mario World (USA).sfc
└── Super Mario World (USA).png
```

要对同一游戏使用每种艺术作品类型，在
所有四个目录中重复匹配的文件名：

```text
ROMS/
├── Super Mario World (USA).sfc
├── Named_Boxarts/
│   └── Super Mario World (USA).png
├── Named_Titles/
│   └── Super Mario World (USA).png
├── Named_Snaps/
│   └── Super Mario World (USA).png
└── Named_Logos/
    └── Super Mario World (USA).png
```

您也可以在 ROM 目录中添加额外的自定义图像：

```text
Super Mario World (USA)-1.png
Super Mario World (USA)-2.png
...
Super Mario World (USA)-9.png
```

在浏览器中，按 **□** 按以下顺序循环切换可用图像：
ROM 旁边的 PNG → 包装盒艺术 → 标题画面 → 游戏截图 → 标志 → 附加内容
`-1` 到 `-9`。缺失的类型自动跳过。

> Libretro 将缩略图文件名中的 ``&*/:`<>?"\|`` 替换为 `_`。
> 手动安装艺术作品时，将下载的 PNG 重命名为
> ROM 的准确基本名称。下面描述的自动下载器
> 会为您处理此转换。

### 3. USB、MX4SIO、MMCE、HDD、记忆卡、SMB 和 CDFS

目录布局在每个设备上完全相同；只有路径前缀
不同：

| 设备 | 示例 ROM 目录 |
|--------|-----------------------|
| USB 驱动器、USB HDD/SSD 或 MX4SIO | `mass0:/ROMS/` 或 `mass1:/ROMS/` |
| 标准记忆卡 | `mc0:/ROMS/` 或 `mc1:/ROMS/` |
| MemCard PRO 2 / SD2PSX (MMCE) | `mmce0:/ROMS/` 或 `mmce1:/ROMS/` |
| 内置 APA/PFS HDD | `hdd0:/PARTITION/ROMS/` |
| 只读网络共享 | `smb:/ROMS/` |
| 光盘或 ISO | `cdfs:/ROMS/` |

对于 USB、HDD、MMCE、记忆卡或 SMB，使用上面显示的布局将 ROM 和 `Named_*`
目录复制到设备/共享。浏览器在进入目录时会索引 PNG 文件，避免每次选择更改时进行缓慢的存储扫描。有关所需的 `SMB.CNF` 和服务器设置，请参阅 [SMB ROM 加载](#smb-rom-loading-replaces-host)。

`make covers` 和 `COVER=y` 还会在 ROM 旁边生成一个小的 `COVERS.IDX`。它让 CDFS 和其他慢速设备加载一个顺序索引，而不是枚举所有四个艺术作品目录。`COVERS.IDX` 和 `Named_*`
目录会从游戏列表中隐藏。没有索引的手动布局
仍然通过兼容的目录扫描回退工作。

对于 **CDFS**，首先在计算机上准备一个普通目录，并将其传递给
Makefile。所有现有的 PNG 文件和子目录都会保留在
`cdfs:/ROMS/` 下：

```bash
make iso ROMS=/path/to/ROMS OUT=/path/to/output
```

另一个选项是 ELF 旁边共享的 `covers/` 目录。它可以包含
普通 PNG 文件和/或四个 `Named_*` 目录。要使用不同的共享路径编译，请使用：

```bash
make clean
make COVERS_PATH=mass:/SNESticle/covers
```

共享路径首先被搜索。ROM 目录和 ELF 旁边的 `covers/`
仍然作为回退位置可用。

### 4. 使用 `COVER=y` 自动下载

构建 ISO 时，`COVER=y`（或 `cover=y`）根据 ROM 扩展名检测 SNES/NES 游戏，尽可能检查 `.zip` 内容，并在 Libretro 中搜索所有四种艺术作品类型：

```bash
make iso \
  ROMS=/path/to/ROMS \
  OUT=/path/to/output \
  COVER=y
```

- `COVER=n` 是默认值：从不访问网络。
- 匹配尝试精确名称、Libretro 的 `_` 字符替换、没有尾部标签的名称以及常见的区域别名，如 `(U)` → `(USA)`。
- 现有有效的艺术作品永远不会再次下载或覆盖。
- 未知 ROM 和没有匹配项的游戏会被报告并跳过；ISO
  创建继续。
- PNG 文件仅添加到临时 ISO 树中。原始 ROM 字节
  永远不会被更改。
- 自动生成紧凑的 `COVERS.IDX` 以减少浏览艺术作品时的暂停，
  尤其是在 CDFS 上。
- 使用 `COVER_SYSTEM=snes` 或 `COVER_SYSTEM=nes` 在无法自动识别 `.zip` 时强制指定系统。`COVER_JOBS=6` 控制并行下载的数量。

要为任何其他设备准备目录而不创建 ISO：

```bash
make covers ROMS=/path/to/ROMS
```

此命令在每个包含 ROM 的目录内创建 `Named_Boxarts`、`Named_Titles`、`Named_Snaps` 和
`Named_Logos`。它仅添加 PNG 文件；
ROM 不会被重命名、打开写入或修改。在手动添加或移除艺术作品后再次运行它
以刷新 `COVERS.IDX`，或者仅删除
`COVERS.IDX` 让模拟器自行扫描目录。

**支持的格式：** 8 位或 16 位 RGB/RGBA PNG、灰度 PNG 和
1/2/4/8 位调色板/索引 PNG。**不支持 Adam7 隔行扫描**，会被下载器自动拒绝。对于手动准备的艺术作品，保存时不要使用隔行扫描，并优先选择大约 256 px 的图像以减少内存使用和解码时间。

</details>

---

## 🎵 菜单音乐与音频

<details>
<summary>显示详情</summary>


ROM 浏览器和暂停菜单中播放背景音乐 —— 追踪器模块
格式为 **`.mod`**（Amiga ProTracker）和 **`.xm`**（FastTracker II），由捆绑的官方 [**libxmp-lite 4.7.2**](https://github.com/libxmp/libxmp/releases/tag/libxmp-4.7.2)
源代码在 EE 上解码。它应用
原始的 ProTracker/FastTracker 效果、节奏、乐器和循环规则，并
包含旧 4.5.0 PS2 分支之后的上游修复，包括一个可能导致音符消失的通道取消静音回归。经典 MOD 还会在加载前设置上游推荐的默认声像为 50；这可以防止
硬左/右声道的乐器在不完美的 HDMI/TV 单声道下混中听不见。
此设置仅影响菜单追踪器音乐，不影响 SNES 或 NES 游戏音频。

将一个或多个曲目放入以下任一文件夹。记忆卡和大容量存储
文件夹会立即索引；已启用的 MMCE/HDD 源通过它们自己的设备探测检查一次。CD/DVD 稍后使用非阻塞就绪轮询检查，因此在驱动器仍在检测光盘时启动 ISO 不会卡住。支持子文件夹（最多四级），
并且生成的索引会被缓存：

- `BGM_PATH` 文件夹（如果您在构建时指定了一个 —— 见下文）
- `mc0:/SNESticle/bgm`、`mc1:/SNESticle/bgm`
- `mmce0:/SNESticle/bgm`、`mmce0:/bgm`
- `mmce1:/SNESticle/bgm`、`mmce1:/bgm`
- `mass:/SNESticle/bgm`、`mass:/bgm`
- 第一个包含 `SNESticle/bgm` 或 `bgm` 的已启用内置 HDD APA/PFS 分区
  （例如 `hdd0:/+OPL/SNESticle/bgm`）
- `cdfs:/BGM`（ISO 内）

MMCE 文件夹仅在 **MMCE Cards** 启用且仅在响应硬件 PING 的端口上扫描。在视频配置中启用 MMCE 会在当前会话期间触发一次性扫描，因此无需重新打开自制程序即可发现其曲目。

内置 HDD 文件夹仅在 **HDD Support** 启用时扫描。该驱动默认关闭；一旦启用，播放器会枚举主 PFS 分区并停在第一个包含曲目的分区。播放列表条目保留其完整的 `hdd0:/PARTITION/...` 身份，因此在浏览器中打开另一个 HDD 分区不会破坏下一首曲目。

启动时会随机选择 **一首曲目**，并且每次离开游戏返回菜单时（当存在多首曲目时）选择 **另一首不同的曲目**。

**视频配置 → 音频**

| 选项 | 范围 | 说明 |
|--------|-------|-------|
| **游戏音量** | 0–100 | 模拟 SNES/NES 音频的响度。**100 = 默认**（匹配 Snes9x）；0 静音。适用于两个核心。 |
| **菜单音乐** | 关闭 / 1–100 | 背景音乐音量。**0 = 关闭** —— 播放器未加载且不使用 RAM。在等待 CD/DVD 检测时显示 **搜索中**，仅当未找到可播放的 `.mod`/`.xm` 时显示 **无曲目**。 |
| **频率** | 16–48 kHz | 菜单音乐的合成速率（输出始终重采样到 48 kHz）。越高音质越好但 CPU 占用更多；**24 kHz** 是默认值，也是稳定帧率的最安全设置。 |

PS2 上菜单侧的文件系统调用是同步的。当浏览器正在枚举大文件夹、读取/解码封面、写入设置文件或 SMB 正在连接时，一个小型 EE 辅助线程仅为已加载的追踪器和 `audsrv` 提供服务；它本身从不打开或扫描文件系统。模态消息也会继续更新播放器。辅助线程在这些 I/O 范围之外完全休眠，包括游戏期间。更改合成频率会在内存中重启已加载的 libxmp 播放器，而不是从磁盘重新读取模块。

这三项设置都会持久化到记忆卡（按 ✕ 保存），并且对 SNES 和 NES 工作方式相同（菜单和音频路径是共享的）。

要在构建中固化默认曲目文件夹或合成速率：

```bash
make BGM_PATH=mass:/snes/bgm    # 首先在哪里查找 .mod/.xm
make BGM_PATH=hdd0:/+OPL/SNESticle/bgm
make BGM_RATE=24000             # 16000/22050/24000/32000/38000/44100/48000
```

构建 ISO 时，添加 `bgm=`（或 `BGM=`）来捆绑一个曲目文件夹（它们会
放入 `cdfs:/BGM`）。如果该文件夹不包含
`.mod`/`.xm`，构建会报错停止，从而防止意外生成无音乐的 ISO：

```bash
make iso roms=/path/to/roms bgm=/path/to/tracks
```

> **许可证：** `libxmp-lite` 采用 MIT 许可。供应商源代码、许可证和
> 确切的 PS2 移植版本位于 `src/third_party/libxmp-lite/` 下。

</details>

---

## 💾 存储与设备

<details>
<summary>显示详情</summary>


ROM 浏览器立即列出本地设备。可选硬件和网络
源在 **视频配置 → 存储 / 设备** 中启用，并且仅在
被选中时初始化，因此缺少 HDD、MMCE 卡、网线或 DHCP 服务器
不会拖慢正常启动。

| 设备 | 是什么 |
|--------|------------|
| `mass0:` / `mass1:` | **USB**（PS2 的两个端口）、USB **外置 HDD/SSD** 和 **MX4SIO** SD 卡 —— 所有块设备共享 `massN:` 命名空间，按检测顺序编号。 |
| `hdd0:` | **内置 HDD**（PS2 Fat 扩展槽），像 HDD‑OSD / OPL 一样进行 APA 分区。 |
| `mc0:` / `mc1:` | **记忆卡** —— 包括原始 **MemCard PRO**（第一代），其行为如同普通卡。 |
| `mmce0:` / `mmce1:` | **MMCE** 卡带（**MemCard PRO 2**、**SD2PSX**），通过 `mmceman`。 |
| `cdfs:` | 游戏/数据光盘（或此 ELF 被刻录进的 ISO）。 |
| `smb:` | 一个配置好的 **只读 SMB 网络共享**，用于浏览和加载 ROM。 |

**文件系统 / 分区：** 捆绑的 **BDM** 栈（`bdm` + `bdmfs_fatfs` +
`usbmass_bd`）读取 **FAT16 / FAT32 / exFAT**，支持 **MBR 或 GPT** 分区
表（因此大于 2 TB 的驱动器也可以工作），与现代化 OPL 一致。内置
HDD 另外使用 `ps2atad` + `ps2hdd` 来访问 APA `hdd0:` 设备。

### SMB ROM 加载（替代 `host:`）

原始的 iaddis `host:` 条目是用于
**ps2link/ps2client HostFS** 的开发桥梁。它让作者在开发时从 PC 加载 ROM 和 IRX 文件，但它从来不是一个普通的网络共享。其行为取决于启动器或模拟器提供的 HostFS；某些实现返回不可靠的类型元数据，使常规文件显示为目录。
因此 `host:` 不再显示在用户 ROM 浏览器中。内部的直接 ELF/ps2link 启动回退仍然可供开发人员使用。

SNESticle 现在嵌入 PS2SDK 的 `smbman` 并将一个配置好的共享暴露为
`smb:`。网络模块、DHCP 和登录仅当您明确连接
或选择 `smb:` 时才启动；仅仅启动或打开设置选项卡不会触及 DEV9。

#### NetherSX2 2.2n+ DEV9 设置

在 **API** 和 **Device** 显示 **Not Set / Não definido** 的截图中，
虚拟 PS2 没有网络链路，SNESticle SMB 无法访问 DHCP 或
服务器。在 NetherSX2 中打开 **App Settings → Settings → System → Networking** 并使用：

| NetherSX2 设置 | 普通家庭网络的值 |
|--------------------|---------------------------------|
| **启用 DEV9 以太网** | 开 |
| **API** | `Sockets` |
| **Device** | 当 Android 设备和 SMB PC/NAS 在同一 Wi-Fi/LAN 上时使用 `WiFi`；仅当手机通过 VPN 路由连接时使用 `VPN`，或者为移动数据使用相应的 `SIM DATA` 选项。 |
| **DNS1 / DNS2 模式** | `Auto (Default)` |
| **DNS1 / DNS2** | `0.0.0.0 (Default)` |

该屏幕中的手动 DNS 预设是为 PS2 在线游戏服务器准备的；
SNESticle 使用其 SMB 网络选项卡中输入的数值 **服务器 IP**，不需要这些。应用设置后，完全重启模拟的 PS2，
然后在 SNESticle 中配置 **服务器 IP**、**共享**、用户/密码和端口。
[官方 NetherSX2 2.2n 发布说明](https://github.com/Trixarian/NetherSX2-patch/releases/tag/2.2n)
记录了相同的 DEV9 `Sockets` 和设备选择。

#### 推荐：在 PS2 上配置

使用 L1/R1 打开 **SMB 网络** 选项卡（原 Host 屏幕），然后设置：

| 字段 | 含义 |
|-------|---------|
| **服务器 IP** | PC/NAS 的数值 IPv4 地址。按 Cross，用左/右选择一个八位组，用上/下更改它。 |
| **端口** | Cross 或左/右在常用端口 445 和 139 之间切换。 |
| **共享** | 仅共享名称，例如 `roms`；它不是本地文件系统路径。 |
| **用户名** | SMB 账户，或 `GUEST` 用于访客共享。 |
| **密码** | 访客访问留空。屏幕上会掩码显示。 |

对于共享/用户名/密码，按 Cross 编辑，左/右移动
光标，上/下选择字符，Cross 前进/添加，Square 删除，
Triangle 完成。完成后选择 **保存并连接**。模拟器
自动验证并写入 `mc0:/SNESticle/SMB.CNF`（回退到
`mc1:`）。如果两张卡都不可写，它会尝试可写的 ELF 目录、
`mass0:`/`mass1:`/旧版 `mass:`（USB 或 MX4SIO），然后是已启用且检测到的
`mmce0:`/`mmce1:` 存储。从内置 HDD PFS 分区启动的 ELF
也可以在其旁边保存。然后启用 SMB 并尝试
连接。屏幕上会显示确切的保存路径和具体的连接错误。
Circle 重新加载保存的文件。

#### 高级：手动创建 `SMB.CNF`

您可以复制并编辑 [`SMB.CNF.example`](SMB.CNF.example)：

```ini
SERVER_IP=192.168.1.100
SERVER_PORT=445
SHARE=roms
USER=GUEST
PASSWORD=
PASSWORD_TYPE=-1
```

`SERVER_IP` 必须是数值 IPv4 地址。`SHARE` 是共享名称，不是
文件系统路径。密码模式：`-1` 表示访客/无密码，`0` 表示旧版
明文，`1` 表示在认证前本地哈希提供的密码。
如果提供了非空密码但没有 `PASSWORD_TYPE`，则使用模式 `1`。
兼容的 wLaunchELF 名称 `smbServer_IP`、`smbServer_Port`、
`smbUsername`、`smbPassword`、`smbPasswordType` 和 `smbShare` 也被
接受。

手动文件按以下顺序搜索：

- `mc0:/SNESticle/SMB.CNF` 或 `mc1:/SNESticle/SMB.CNF`；
- 可写的独立 ELF 旁边；
- `mass0:/SNESticle/SMB.CNF`、`mass1:/SNESticle/SMB.CNF`，然后在启用 Mass/USB 或 MX4SIO 支持时的旧版
  `mass:/SNESticle/SMB.CNF` 别名；
- 对响应硬件探测的已启用 MMCE 插槽使用 `mmce0:/SNESticle/SMB.CNF` 或 `mmce1:/SNESticle/SMB.CNF`；
- 兼容的共享 `mc0:/SYS-CONF/SMB.CNF` 或
  `mc1:/SYS-CONF/SMB.CNF` 回退；
- 光盘/ISO 根目录作为 `cdfs:/SMB.CNF`。

主机上的设置会更新其加载的可写文件，或者仅创建模拟器拥有的 `SNESticle/SMB.CNF` 回退。它永远不会覆盖 `SYS-CONF` 下共享的 wLaunchELF 文件。本地存储上模拟器拥有的文件优先于共享/只读捆绑文件，因此更改服务器不需要重新构建 ISO。

对于 ISO 构建，可以自动将其复制到根目录：

```bash
make iso ROMS=/path/to/roms SMB_CONFIG=/path/to/SMB.CNF
```

对于手动文件，启用 **视频配置 → SMB（网络）**，用 Cross 保存并在浏览器中打开 `smb:`。SMB 网络选项卡中的 **保存并连接** 操作会自动启用该选项。**无 SMB.CNF**、**DHCP 超时**、**认证错误** 或 **共享错误** 等状态可识别失败的阶段。

浏览器故意在 `smb:` 上禁用复制、粘贴和删除。ROM、ZIP 及其 PNG 艺术作品从共享中读取；SRAM 和即时存档仍
保存到配置的本地记忆卡/USB 目标。也将服务器共享本身配置为只读。一个最小的 Samba 共享是：

```ini
[roms]
    path = /srv/ps2-roms
    browseable = yes
    read only = yes
    guest ok = yes
```

> `smbman` 实现 **SMB1/NT1**，而非 SMB2/3。如果服务器需要，
> `server min protocol = NT1` 是 Samba 的全局设置。SMB1 已过时，在不可信网络上不安全：请在隔离/可信的局域网上使用专用的只读共享，切勿暴露到互联网。模拟器还需要正常工作的 DEV9/SMAP 以太网仿真；仅提供 HostFS 是不够的。

> **构建说明：** 完整的 USB 组（`usbd_mini`、`bdm`、
> `bdmfs_fatfs`、`usbmass_bd`）和 SIO2 组（`sio2man`、记忆卡、
> pad/multitap、MMCE 和 MX4SIO）被固定在 [`irx/`](irx/) 下。构建不再
> 根据安装的 PS2SDK 不同而改变 USB 行为。USB 使用 OPL 为其 BDM 加载器选择的基于 FreeUsbd 的 `usbd_mini`；浏览器还会在慢速驱动器完成挂载时最多重试选定的 `massN:` 三秒钟。内置 HDD 模块仍由 PS2SDK 提供，因为仅在启用 HDD 支持时才加载。
> MMCE 插槽仅在硬件 PING 成功后列出。
>
> MMCE 和 MX4SIO 都挂接 SIO2，因此互斥。打开
> 一个会关闭另一个设置。如果相反的驱动已经
> 驻留，视频配置会显示 **重启**，并在下次启动时安全地应用更改。
>
> 每个存储模块在启动画面上打印其加载结果
> （`bdm.irx = 0`、`hdd (hdd0:) = N`、……），因此失败在屏幕照片中可见。
> 在没有内置 HDD 的主机上，`dev9`/`hdd` 探测只会
> 报告“无硬件”并继续启动——不会挂起。

</details>

---

## 🔨 构建（PlayStation 2）

<details>
<summary>显示详情</summary>


您需要安装 **PS2SDK**。按照
[ps2dev](https://github.com/ps2dev/ps2dev.git) 的说明并使用
**最新** 的 PS2SDK。

```bash
cd ~/SNESticleRevive

# 仅构建 ELF
make                 # 单工作线程
make JOBS=3          # 并行构建（3 个工作线程）

# 使用 ROM 文件夹构建可启动 ISO 并复制所有内容
make iso ROMS=/path/to/roms OUT=/path/to/output JOBS=3

# 查看所有选项
make help

# 清理构建文件夹
make clean
```

生成 `SNESticle.elf`（以及 `iso` 目标对应的打包 ELF / ISO）。

### 方便的构建标志

| 标志 | 作用 |
|------|--------------|
| `JOBS=N` | 并行编译工作线程数（`make iso` 也支持）。 |
| `VERBOSE=1` | 显示 **完整** 的警告/错误文本（不截断）。 |
| `PROFILE=1` | 编译进屏幕上的性能分析器 —— 在游戏中按 **R3** 捕获一帧的每段计时。 |
| `OUT=/path` | 将最终 ELF/ISO 复制到此文件夹。 |
| `ROMS=/path` | 构建 ISO 时要嵌入的 ROM 文件夹。 |
| `COVER=y` / `cover=y` | 下载匹配的 Libretro 包装盒艺术/标题/截图/标志到 ISO 中；`n` 是离线默认值。 |
| `COVER_SYSTEM=auto` | 自动检测 SNES/NES；使用 `snes` 或 `nes` 覆盖模糊的压缩包。 |
| `COVER_JOBS=6` | 并行缩略图下载数。 |
| `PACK=0` | 使用未打包的 ELF 构建 ISO。 |
| `COVERS_PATH=path` | 固化到构建中的共享封面艺术文件夹（例如 `mass:/snes/covers`）。见 [封面艺术](#-封面艺术)。 |
| `BGM_PATH=path` | 首先扫描菜单音乐 `.mod`/`.xm` 文件的文件夹。见 [菜单音乐与音频](#菜单音乐与音频)。 |
| `BGM_RATE=hz` | 默认菜单音乐合成速率（例如 `32000`）。 |
| `SMB_CONFIG=/path/SMB.CNF` | 将单共享 SMB 配置复制到 ISO 根目录，而不打印其凭据。 |

> 注意：更改像 `PROFILE=1` 这样的标志本身**不会**强制重新编译
> （make 只跟踪文件时间戳）。切换编译标志时，请先运行 `make clean`。

</details>

---

## 📝 近期完成的工作

<details>
<summary>显示详情</summary>


当前测试版本的累积说明可在
[`CHANGELOG_v1.0.4.md`](CHANGELOG_v1.0.4.md) 中找到。


- **协处理器**：添加了 DSP‑1、DSP‑2、CX4、OBC1、S‑DD1 和 S‑RTC，每个
  均为净室编写，并在主机端针对公共参考进行了位精确验证。
  **DSP‑4**（顶级赛车 3000）是 **HLE / 独立**（无外部文件）：总线协议加上完整的赛道投影数学来自 **ZSNES** DSP‑4
  HLE（GPLv2，© ZSNES 团队 — zsKnight、_Demo_、pagefault、Nach），已移植并注明出处。纳入 GPLv2 代码正是该项目从 MIT 重新授权为 **GPLv2** 的原因。
- **NES (InfoNES) 集成**：完整的 PS2 平台层（渲染、输入、音频、
  单帧步进器）。五个基础 2A03 通道现在使用 Shay Green 的
  周期定时 **Nes_Snd_Emu + Blip_Buffer**，频率为 32 kHz，视频使用 Mesen2 的
  默认 NTSC 2C02 调色板，而不是旧的饱和 RGB555 表。
- **视频**：迁移到 gsKit，新增视频配置屏幕、多种模式，以及
  **安全的 480i 默认值**（原生 256x240 仍可供 CRT 用户使用）。
- **封面艺术**：ROM 浏览器显示自定义图像以及来自 PNG 文件的 Libretro 包装盒艺术、
  标题画面、游戏截图和标志，以及 `-1` 到
  `-9` 的手动附加内容。解码后的封面被缓存，邻近项被预取，因此
  即使从 CD 浏览也能保持流畅。`make iso ... COVER=y` 获取所有
  匹配的艺术到 CDFS 中，而不触及源 ROM；`make covers
  ROMS=...` 为任何其他设备准备相同的 `Named_*` 布局。
- **ROM 浏览器**：将 CDFS、USB、记忆卡、SMB、MMCE 和 PFS/HDD 切换到
  直接目录记录，消除了逐文件 `stat` 往返，这
  使得大型 CDFS 文件夹尤其缓慢。iomanX 标准化的 `FIO_S_*` 模式
  位可在每个设备上一致地识别目录；来自不寻常
  第三方驱动且不报告类型的条目通过完整路径检查。PS2SDK 的 CDFS 驱动的一个流式分支移除了其固定的 256 条目 ISO 表，EE 条目数组按需增长，并且列表/青绿色页脚具有独立的几何结构，因此长目录永远不会覆盖状态文本。
- **菜单音乐与音频控制**：追踪器音乐（`.mod` / `.xm`）通过 **libxmp-lite** 的 PS2 移植在
  ROM 浏览器和暂停菜单中播放，在 EE 上解码并持续重采样到 SPU2 的 48 kHz。添加了 **游戏音量**、
  **菜单音乐** 音量（0 = 关闭，释放其 RAM）以及视频配置中的合成 **频率**
  选择器 —— 全部持久化，SNES 和 NES 共享。启动时随机播放一首曲目；从游戏返回时恢复已加载的解码器而无需重新加载磁盘，并且播放列表在曲目完成时前进。一个休眠的 EE 辅助程序防止目录、封面、设置和 SMB I/O 饿死菜单流。
- **存储**：固定的 **BDM** 栈搭配 OPL 基于 FreeUsbd 的 `usbd_mini`
  取代了旧的单 USB 路径 —— 两个 USB 端口、外置 HDD/SSD 和
  MX4SIO 都以 `mass0:`/`mass1:` 出现，读取 FAT16/FAT32/exFAT，支持
  MBR/GPT。慢速 USB 介质获得有界的挂载重试。添加了内置
  HDD（`hdd0:`，APA）和 MMCE 卡带（`mmce0:`/`mmce1:`，MemCard PRO 2 / SD2PSX）。
  不可靠的面向用户的 `host:` 设备被一个惰性、只读的 `smb:` ROM 共享取代，具有有界的 DHCP/登录错误和正确的文件类型。
  参见 [存储与设备](#存储与设备)。
- **启动 / 输入**：控制器和 IRX 初始化重做，以在真实
  硬件上表现正常，而不仅仅是在模拟器中。直接 ELF 启动也能容忍省略可执行路径的启动器，而不是在视频初始化前崩溃。
- **即时存档**：恢复了休眠的 iaddis 时代功能，作为发布菜单
  提供五个存档位、USB/记忆卡选择、版本化文件、ROM/CRC 检查
  和断电安全的双银行写入；v1.0.4 还序列化了 NES CPU、PPU、
  pAPU、CHR RAM 和完整的 mapper 私有状态。
- **构建系统**：并行任务、`VERBOSE`、`PROFILE`、更友好的 `make help`，
  以及支持 `JOBS` 的 ISO 构建。
- **错误修复**：清理了 C++17 / 构建警告，修复了 InfoNES 核心中的三个真实的越界错误（`APU_Reg`、mapper 19 和 45 数组）
  以及 6502 核心中的一个序列点未定义行为。

</details>

---

## 🐞 已知问题 / 仍缺失

<details>
<summary>显示详情</summary>


**SNES**
- 即时存档目前仅支持基础硬件游戏；协处理器游戏
  会被阻止，直到每个额外芯片都有完整的序列化。
- **Final Fight 2** —— 修正矩形 OBSEL 6/7 尺寸是必要的
  但并未单独修复报告的游戏场景。r17 移除了 GIF-DMA 竞争，该竞争让 GS 在 CPU 重用其源缓冲区时读取扫描线，在 64 KiB 银行回绕时保留 DMA 模式相位，并将镜像 OBJ 瓦片获取顺序与 34 瓦片硬件限制对齐。r18 另外修复了 65816 的 24 位总线回绕，此前官方向量暴露了超过 `$FF:FFFF` 的索引读取落入空页。OAM/VRAM 突发路径和 CPU 回绕具有主机覆盖率。r19 审计了仿真/原生模式下的每个 65816 操作码，修复了堆栈/直接页/十进制/中断边界情况，并将 DMA/HDMA 时序与 MesenCE/Mesen2 对齐。原始场景仍需要 PS2/NetherSX2 重新测试才能被视为已修复。
- **First Samurai / Final Fight 3** —— r20 用 S-PPU 单独的横向、
  共享 H/V 和 Mode 7 锁存器替换了旧的 `$210D-$2114` 低/高对近似。r21 深度捕获随后暴露了 First Samurai 马赛克的直接原因：模式 4 MDMA 将 `$2116/$2117` 地址写入排队，但立即应用了 `$2118/$2119` 数据，因此瓦片字落到了陈旧的 VRAM 地址。MDMA PPU 端口写入现在保留传输顺序，并有一个聚焦的主机回归测试。受影响的场景在标记为已修复之前仍需要 PS2/NetherSX2 视觉重新测试。
- **Wild Guns** —— r19 实现了 MDMA 后文档记录的 24 时钟 NMI 延迟，并纠正了 HDMA 状态机/模式 5。角色选择后报告的整个屏幕闪烁在问题标记为已修复之前仍需要在相同的 NetherSX2 设置上确认。
- 一些大型 / 特殊芯片游戏可能仍然冻结或行为异常。
- **SuperFX (GSU)** 在 v1.0.4 中是实验性的：r15 纠正了缓存窗口旋转、可执行 RAM 银行 `$60-$7F`、字节 MMIO 和热循环，但 Star Fox/Yoshi 和其他板卡仍需要逐游戏 PS2 验证。
- **缺失芯片**：SA‑1 未实现。

**NES (InfoNES)**
- 即时存档目前涵盖 `.nes` 卡带；FDS 状态序列化
  不可用。
- **性能**：繁重场景可能使一帧超出 16.6 ms 预算，vsync
  随后锁定到 **30 fps**；这也会使音频和每扫描线
  效果不同步。（使用 `PROFILE=1` + R3 定位热点。）
- **Super Mario Bros 3** —— 滚动时 MMC3 状态栏分割可能出现故障
  （InfoNES 使用扫描线近似 MMC3 IRQ，而非 A12 精确）。
- 五个基础 2A03 通道已实现。一些日本/特定 mapper 版本使用的扩展音频（VRC6、VRC7、MMC5、FDS 和 Sunsoft 5B）尚未连接，因此这些版本仍可能缺少乐器。

**视频**
- 视频配置屏幕仅暴露 **480i** 和 **1080i**。保存为 240p/288p 或 480p 的旧设置会自动迁移到 480i。

> 某些错误仅在 **真实 PS2 硬件** 上重现（像 NetherSX2 /
> PCSX2 这样的模拟器更宽容），这使得它们更难追踪。

</details>

---

## 📂 项目布局

<details>
<summary>显示详情</summary>


```
src/snes/      SNES 核心（cpu、spc、ppu、协处理器）
src/nes/       NES 核心（InfoNES：核心、cpu、apu、mappers、系统）
src/platform/  PlayStation 2 平台（gs、系统、输入、ui）
src/modules/   共享模块（音频、网络对战等）
src/common/    共享辅助工具（渲染、基础、io、调试）
tools/         主机端测试工具（芯片 + OBJ 验证）
```

</details>

---

## ❤️ 致谢

<details>
<summary>显示详情</summary>


- **[iaddis/SNESticle](https://github.com/iaddis/SNESticle)** — Icer Addis，原始模拟器。
- **[nesdev-org/MesenCE](https://github.com/nesdev-org/MesenCE)** — 当前
  Mesen2 衍生的 65816、中断和 SNES DMA/HDMA 行为参考，由 r19 审计使用。
- **[SingleStepTests/65816](https://github.com/SingleStepTests/65816)** —
  完整的逐操作码状态、内存和总线周期向量，由
  `tools/cputest` 使用。
- **[ZSNES 团队](https://www.zsnes.com)** — zsKnight、_Demo_、pagefault、Nach；他们的 GPLv2 DSP‑4 HLE（`chips/dsp4emu.c`）在此移植为 `src/snes/core/dsp4emu.*`（顶级赛车 3000 支持）。
- **[tmaul/SNESticle](https://github.com/tmaul/SNESticle)** — 许多后续改进。
- **[Wolf3s/SNESticle](https://github.com/Wolf3s/SNESticle)** — 用作此仓库基础之一的分支。
- **Sardu** — 于 2022 年以 MIT 许可证发布恢复的源代码。
- **[jay-kumogata/InfoNES](https://github.com/jay-kumogata/InfoNES)** — 此处集成的 NES 核心。
- **[game-music-emu](https://github.com/libgme/game-music-emu)** — Shay Green 的 Nes_Snd_Emu 和 Blip_Buffer 用于五个周期定时的基础 NES 音频通道（LGPL-2.1+）。
- **[Mesen2](https://github.com/SourMesen/Mesen2)** — NES 渲染器使用的参考/默认 NTSC 2C02 调色板。
- **[upng](https://github.com/elanthis/upng)** — Sean Middleditch 和 Lode Vandevenne；捆绑的单文件 PNG 解码器用于封面艺术（zlib 许可证）。在此仓库中扩展了调色板/索引支持。
- **[libxmp-lite](https://github.com/libxmp/libxmp)** — Claudio Matsuoka 和 Hipolito Carraro Jr；官方 4.7.2 嵌入式 MOD/XM 重放源代码用于追踪器效果、时序和循环（MIT）。tatokis 的早期 PS2 集成是原始移植参考。
- **[hugorsgarcia/PS2SNESticle](https://github.com/hugorsgarcia/PS2SNESticle)** — **Hugo Garcia**，他的 PS2 工作是控制器 / 记忆卡 / IRX 初始化和网络对战模块的参考。
- **Open‑PS2‑Loader**、**picodrive‑PS2** 和 **uLaunchELF** — 正确 PS2 启动、IOP 和视频行为的参考。
- **ReyFxck** — 此复活/分支及持续开发。
- **Adriano Oliveira** — 真机测试。
- **控制提示图标** (`docs/controls/*.svg`) — 为本仓库绘制的原始 SVG；可自由重用。


</details>

---

## 📜 许可证

<details>
<summary>显示详情</summary>


**GNU GPL v2** — 见 [`LICENSE`](LICENSE)。

原始 SNESticle 源代码（Icer Addis，2022）采用 MIT 许可；MIT
允许重新授权，因此此分支以 GPLv2 分发组合作品，以纳入 ZSNES DSP‑4 HLE（GPLv2）。Icer Addis 部分的原始 MIT 通知在 [`LICENSE`](LICENSE) 中逐字保留。

- 版权所有 (c) 2022 Icer Addis (iaddis) — 原始 SNESticle 源代码
- 版权所有 (c) 2026 ReyFxck — SNESticleRevive 分支
- DSP‑4 HLE (`src/snes/core/dsp4emu.*`): © 1997–2008 ZSNES 团队 (GPLv2)

</details>

---

# 是的，我们仍然有很多空闲时间 :)
