# 🕵️‍♂️ Stealth Glass Translator（隐形翻译玻璃板）

> **对截图、OBS 录屏、Zoom / 腾讯会议 / Teams 屏幕共享完全隐形的实时悬浮翻译工具。**  
> An invisible floating translation overlay for Windows — visible to your eyes, completely invisible to screen capture, OBS, Zoom, Teams, and screenshots (`WDA_EXCLUDEFROMCAPTURE`).

![Platform](https://img.shields.io/badge/Platform-Windows%2010%20(2004%2B)%20%7C%2011-0078D6?logo=windows)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![OCR](https://img.shields.io/badge/OCR-RapidOCR%20%7C%20Windows%20OCR-success)
![LLM](https://img.shields.io/badge/Translator-DeepSeek%20%7C%20OpenAI%20%7C%20Google-blueviolet)
![License](https://img.shields.io/badge/License-MIT-green)

---

## ✨ 为什么做这个工具？(Why?)

在日常**屏幕共享演示、跨国会议、直播、阅读外文文献/简历/生肉游戏**时，我们经常需要实时翻译屏幕上的内容，但传统翻译软件有两个痛点：
1. **暴露隐私 / 干扰观众**：共享整个桌面时，弹出的查词窗口会被所有人看到。
2. **自遮挡死循环**：普通悬浮原位翻译窗口在截屏 OCR 时，会把自己的翻译文字也截进去，必须频繁闪烁隐藏才能截到背景。

**Stealth Glass Translator** 利用 Windows DWM 底层 API `SetWindowDisplayAffinity(hwnd, WDA_EXCLUDEFROMCAPTURE)` 彻底解决了这两个问题：
- **对他人隐形**：在你的物理显示器上清晰可见，但在系统截图（`Win+Shift+S`）、OBS 录屏、腾讯会议/Zoom/Discord 屏幕共享画面中**完全透明穿透，仿佛不存在**。
- **天然穿透截屏**：因为系统截屏会自动跳过这块“玻璃板”，程序直接截取板子所在区域时，拿到的**永远是板子背后干净的原始屏幕画面**，实现零闪烁的丝滑实时原位翻译！

---

## 🎯 三种形态，按需选择 (3 Modes Included)

仓库内置了三种不同交互形态的隐形翻译器：

| 模式 | 启动脚本 | 适用场景 | 核心特点 |
| :--- | :--- | :--- | :--- |
| 🥇 **翻译玻璃板 (推荐)** | `start_glass.bat` (`glass_translator.py`) | 阅读 PDF/网页/文档/外文软件 | 一块可自由拖动缩放的**半透明磨砂玻璃板**，框住哪里就实时把背后的外文按段落翻译并对齐显示在板子上 |
| 🖥️ **全屏/半屏原位覆盖** | `start_screen.bat` (`screen_translator.py`) | 整个软件界面汉化、游戏/PPT 翻译 | 全屏或半屏完全透明+鼠标穿透，自动提取原文背景色打底，把译文直接贴在原文字位置上 |
| 📋 **轻量剪贴板悬浮窗** | `start.bat` (`stealth_translator.py`) | 零依赖极简查词、复制即翻 | **仅用 Python 标准库（无需 pip）**，监听 `Ctrl+C` 自动翻译或手动输入翻译 |

---

## 🔥 核心特性 (Features)

- 🛡️ **系统级防截屏/防录屏 (`0x11`)**：基于 Windows 10 (2004+) / Windows 11 DWM 内核级保护，不出现在任务栏与 `Alt+Tab` 列表中。
- ⚡ **单键 0 毫秒瞬切隐藏**：按下波浪键 `` ` `` 或 `F2`（或鼠标中键点击窗口），0 毫秒将透明度切为 0 并开启鼠标穿透，不销毁句柄、不闪烁、不丢失防截屏状态。
- 🧠 **本地高精度 OCR + 智能段落合并**：
  - 自动切换 **RapidOCR (PP-OCR)** 与 **Windows 原生 OCR**。
  - 自动将多行折行文本、连字符断词合并为完整自然段再翻译，告别破碎机翻感。
- 🤖 **DeepSeek / OpenAI + Google 双引擎**：
  - 支持接入 **DeepSeek API**（或任意 OpenAI 兼容接口），在翻译的同时根据上下文自动纠错 OCR 识别误差。
  - 未配置 API Key 时自动回退至免费 Google 翻译，开箱即用。
- 💰 **极致省流防抖设计**：
  - 像素级帧差检测：背后画面没变化绝不重复 OCR。
  - 稳定性过滤：拖动窗口或滚动页面过程中不触发 API 请求，画面停稳后才批量翻译，并带有全局内存缓存。
- 🖱️ **鼠标穿透模式 (`Alt+T`)**：开启后鼠标点击直接穿过翻译板操作背后的软件，边看译文边办公。

---

## 🚀 快速开始 (Quick Start)

### 1. 环境要求
- **操作系统**：Windows 10 (版本 2004 / Build 19041 及以上) 或 Windows 11
- **Python**：Python 3.10 ~ 3.13

### 2. 安装依赖
```bash
git clone https://github.com/你的用户名/stealth-translator.git
cd stealth-translator
pip install -r requirements.txt
```
*(注：如果只想使用最轻量的剪贴板悬浮窗 `stealth_translator.py`，无需安装任何依赖，直接运行即可！)*

### 3. 配置 API Key（可选，推荐 DeepSeek）
首次运行会自动生成 `config.json`（也可复制 `config.example.json` 为 `config.json`）：
```json
{
  "deepseek_api_key": "sk-填入你的DeepSeek密钥（留空则使用免费Google翻译）",
  "deepseek_base_url": "https://api.deepseek.com",
  "deepseek_model": "deepseek-chat",
  "target_lang": "auto",
  "ocr_engine": "auto",
  "opacity": 0.78,
  "interval": 0.8,
  "font": "Microsoft YaHei",
  "geometry": "720x360+200+200"
}
```

### 4. 一键启动
直接双击 **`start_glass.bat`**（无黑框后台运行），或命令行执行：
```bash
python glass_translator.py
```

---

## ⌨️ 快捷键一览 (Hotkeys - Glass Mode)

| 快捷键 / 操作 | 功能说明 |
| :--- | :--- |
| **`` ` ``（Esc 下方波浪键）** / **`F2`** / **`Alt + Q`** | **瞬间显示 / 隐藏玻璃板**（0ms 无闪烁） |
| **鼠标中键（按下滚轮）** / **右上角 `👁`** | 在玻璃板上按滚轮或点眼睛图标，瞬间隐藏 |
| **`Alt + E`**（或右上角 `⏯`） | 开启 / 暂停实时识别翻译 |
| **`Alt + R`** | 立即强制重新识别当前区域 |
| **`Alt + T`** | 开启 / 关闭 **鼠标穿透模式**（穿透时可直接点击背后窗口） |
| **`Alt + ↑` / `Alt + ↓`**（或右上角 `+` `−`） | 调节玻璃板透明度（30% ~ 100%） |
| **`Alt + Shift + Q`**（或右上角 `✕`） | 保存窗口位置并退出程序 |

---

## 💡 常见问题与技巧 (FAQ)

<details>
<summary><b>Q1: 如何验证“隐身”是否真的生效了？</b></summary>

1. 看玻璃板左上角状态栏是否显示蓝色的 **`隐身✓`**。
2. 按下 `Win + Shift + S` 系统截图，或者打开 OBS / 腾讯会议开启“共享整个桌面”，你会发现截出来的图里完全没有这个翻译框，只能看到它背后的窗口。
</details>

<details>
<summary><b>Q2: 想要毫秒级极速 OCR 怎么办？</b></summary>

当 `ocr_engine` 设为 `"auto"` 时：
- 如果你的 Windows 仅安装了中文语言包，程序会自动使用本地 **RapidOCR** 模型（识别英文极准，耗时约 0.8~1 秒）。
- 如果你在 `Windows 设置 -> 时间和语言 -> 语言` 中添加安装 **英语(美国)** 语言包（包含光学字符识别功能），程序会自动切换至 **WinOCR**，单次识别仅需 **30~50 毫秒**。
</details>

---

## ⚠️ 免责声明 (Disclaimer)

本项目仅用于技术研究、日常外文资料阅读、跨语言会议辅助及直播/演示时的个人隐私保护。请勿将本工具用于任何违反法律法规、违反平台规定或破坏考试/面试公平性的场合，因不当使用造成的一切后果由使用者自行承担。

## 📄 License

Released under the [MIT License](./LICENSE).
