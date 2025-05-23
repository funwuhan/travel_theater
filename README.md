# 项目开发说明 (Project Development Guide)

本项目遵循 "Vibe Coding" 的核心原则进行开发，旨在创造一个高效、灵活且愉快的协作环境，并最终交付符合预期"感觉"的产品。

## 12条Vibe Coding规则总结

这些规则指导我们的开发流程、沟通方式以及与AI的协作模式：

1.  **从感受/氛围开始项目管理 (Start with vibe PMing)**:
    *   在技术细节之前，明确项目的核心感觉、目标和用户体验。

2.  **保持技术栈简洁 (Keep your tech stack simple)**:
    *   选择真正适合项目需求且易于管理的技术，避免不必要的复杂性。

3.  **给AI正确的规则和文档 (Give AI the right rules & documentation)**:
    *   提供清晰指令、上下文和相关文档，以便AI能有效协助。

4.  **要求AI不要（直接）编码 (Ask AI NOT to code)**:
    *   AI主要辅助规划、提供建议、调试和解释，人类开发者主导编码和决策。

5.  **向AI询问选项并选择简单的那个 (Ask AI for options and pick the simple one)**:
    *   利用AI提供多种方案，从中选择最简单、直接且能满足需求的。

6.  **将任务分解成小步骤 (Break tasks into small steps)**:
    *   将大任务细化为易于管理和执行的小模块，逐步推进。

7.  **用图片给AI提供上下文 (Include images to give AI context)**:
    *   使用视觉材料（如截图、设计稿）帮助AI更直观地理解需求。

8.  **每次更改后都进行严格测试 (Test ruthlessly after every change)**:
    *   每次代码修改后进行彻底测试，尽早发现并修复问题。

9.  **不要犹豫回滚 (Don't hesitate to revert)**:
    *   如果更改导致问题或未达预期，果断恢复到之前的稳定状态。

10. **使用Github进行版本控制 (Use Github for version control)**:
    *   利用Git和GitHub追踪代码变更、协作和管理版本。

11. **用你的声音感受氛围 (Use your voice to feel the vibes)**:
    *   清晰表达对项目方向、设计和功能的真实看法与直觉，确保项目符合预期"vibe"。

12. **让AI解释代码 (Ask AI to explain the code)**:
    *   对于AI生成的或任何不清晰的代码，要求AI进行解释，确保理解。

**核心理念总结:**

这些"Vibe Coding"规则描绘了一种灵活、迭代、注重直觉和体验，并深度融合 AI 协作的开发模式。它强调简单性（规则 2, 5）、迭代与反馈（规则 6, 8, 9）、有效利用 AI（规则 3, 4, 5, 7, 12）、保持开发者理解与控制（规则 4, 12）以及基础工程实践（规则 8, 10）。最终目标是利用 AI 加速开发、激发创意，同时保持开发过程的流畅感和愉悦感（即 "vibe"），并产出符合预期"感觉"的产品。

---

## 项目概述：旅途摄影剧场 (Travel Photography Theater)

### 项目愿景
打造一个沉浸式的旅途摄影展示平台，让用户能够像在剧场中欣赏表演一样，体验每一张照片背后的故事和情感。

### 核心特点
- **沉浸式体验**: 通过精心设计的交互和动画，营造剧场般的观感。**特别关注微信内置浏览器中的全屏体验，力求通过CSS控制完全隐藏浏览器原生UI（尤其是底部导航栏）的干扰。**
- **富媒体展示**: 主要为照片，辅以根据场景的背景音乐和音效。
- **故事性叙事**: 每张照片的不同位置会根据需要覆盖不同的文字描述，如同海报或字幕。
- **轻量级架构**: 基于纯静态网页技术 (HTML, CSS, JavaScript)，确保在轻量服务器上流畅运行。
- **移动优先与响应式设计**: 确保在手机等移动设备上（尤其是微信环境）有良好展示。
- **个性化内容更新**: 允许通过创建新的HTML页面来不定期更新作品集，这些页面将通过 `<iframe>` 嵌入主页。

### 用户需求点 (已确认)
- **作品展示**: 个人摄影作品集，无需分类。
- **交互体验**:
    - 全屏展示模式。
    - 照片平滑过渡切换。
    - 根据不同场景自动播放背景音乐和音效。
- **内容呈现**:
    - 照片上特定位置可叠加文字描述。
- **技术要求**:
    - 响应式设计，主要面向移动端。
    - **微信浏览器真全屏**: 在微信自带浏览器访问时，必须实现无浏览器UI（特别是底部导航栏）的全屏显示。
    - 暂无需照片放大、下载、评论、分享功能。
- **维护方式**:
    - 通过创建新页面更新内容。
    - 无需后台管理系统，纯静态部署。

### 技术架构
- **前端**: 纯静态网页 (HTML5 + CSS3 + JavaScript)。
- **页面嵌入**: `index.html` 作为主容器，使用 `<iframe>` 嵌入各个独立的摄影集/主题页面。
- **静态资源**:
    - CSS和JavaScript文件分别存放于根目录下的 `css/` 和 `js/` 文件夹。
    - 图片、音频、字体等媒体文件存放于 `assets/` 目录。后续也可按需使用腾讯云COS。
- **部署**: 轻量服务器静态文件托管。

### 项目结构 (已更新)
```
travel_theater/
├── index.html              # 主入口页面 (iframe 容器)
├── css/                    # CSS样式目录
│   └── main.css            # 全局和核心样式 (含微信全屏)
├── js/                     # JavaScript脚本目录 (未来使用)
│   └── main.js             # 主要交互逻辑 (iframe切换, 音频等)
├── assets/                 # 媒体资源目录 (图片, 音频, 字体等)
│   ├── images/
│   ├── audio/
│   └── fonts/
├── pages/                  # 各个独立的摄影集/主题页面 (HTML片段)
│   ├── welcome.html
│   ├── collection_A.html   # (示例)
│   └── collection_B.html   # (示例)
└── README.md               # 项目说明文档
```

### 技术实现要点与策略 (根据参考项目 v37chapter 修订)
- **响应式布局**: 针对 `pages/*.html` 内的内容，使用 CSS Grid, Flexbox, 以及 Media Queries。`index.html` 本身主要负责全屏框架。
- **微信真全屏核心策略 (CSS)**:
    1.  **Viewport Meta**: 在 `index.html` 和所有 `pages/*.html` 中使用 `<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">`。
    2.  **全局样式 (`css/main.css`)**:
        ```css
        html, body {
            height: 100%; /* 或 100vh */
            width: 100%;  /* 或 100vw */
            overflow: hidden;
            margin: 0;
            padding: 0;
            overscroll-behavior: none; /* 防止橡皮筋效应 */
            -webkit-overflow-scrolling: touch; /* 可选，有时与overflow:hidden一起使用 */
        }
        /* 进一步确保滚动条不出现 */
        body::-webkit-scrollbar {
            display: none; /* Chrome, Safari, Opera, Edge */
        }
        html {
            -ms-overflow-style: none;  /* IE and Edge */
            scrollbar-width: none;  /* Firefox */
        }
        ```
    3.  **`index.html` 内 `<iframe>` 容器**:
        ```html
        <!-- 在 index.html -->
        <body style="margin:0; padding:0; overflow:hidden;">
            <iframe id="contentFrame" src="pages/welcome.html" style="width:100vw; height:100vh; border:none; margin:0; padding:0; display:block;"></iframe>
        </body>
        ```
    4.  **子页面 (`pages/*.html`) 结构**: 子页面自身也应避免产生内部滚动，内容设计为单屏展示。
    5.  **Fullscreen API (备用/辅助)**: 鉴于参考项目未使用，此API优先级降低。如果纯CSS方法在某些情况下失效，可考虑在用户首次交互时尝试调用 `document.documentElement.requestFullscreen()` 作为辅助。
- **平滑过渡**: 子页面内部的照片切换等，使用 CSS Transitions 或 Animations。`<iframe>` 切换本身可以很简单，或者如果需要也可以加JS控制的淡入淡出。
- **音频控制**: HTML5 `<audio>` 元素和 JavaScript API，考虑在 `index.html`层面统一管理，或由子页面触发。
- **文字覆盖**: 子页面内使用 CSS绝对定位和 `z-index`。
- **图片优化**: 压缩图片大小，考虑使用 WebP 格式。
- **音频优化**: 压缩音频文件。
- **JavaScript 模块化**: 简单的项目可以直接在 `<script>` 标签引入，复杂后可考虑 ES6 Modules 存放在 `js/` 目录下。

### 开发阶段 (修订)
1.  **环境搭建与README初始化** (已完成部分)
2.  **核心需求分析与确认** (已完成部分)
3.  **`index.html` 框架与基础CSS搭建，路径调整** (已完成)
4.  **微信全屏效果验证** (已完成 - 效果良好)
5.  **创建第一个真实内容子页面 (例如 `pages/collection_A.html`)**:
    *   设计页面布局和内容 (例如，单张背景图片 + 文字)。
    *   确保在微信中保持真全屏效果。
6.  **照片展示与切换逻辑 (在子页面中)**:
    *   实现单张图片全屏展示。
    *   简单的左右切换（平滑过渡）。
    *   图片上文字覆盖效果。
7.  **音频集成**:
    *   选择合适的背景音乐/音效。
    *   实现播放控制。
8.  **完善交互与样式**: 根据原型反馈进行调整。
9.  **后续页面开发**: 根据第一个子页面的模板进行扩展。
10. **整体测试与优化**: 跨设备、跨浏览器（重点是微信）测试，性能优化。
11. **部署上线**。

---
> 本说明书将随着项目进展和沟通不断更新完善。 