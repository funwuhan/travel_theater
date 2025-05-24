# 微信内置浏览器全屏解决方案

## 问题背景

在微信内置浏览器中打开网页时，浏览器底部会显示一个导航栏，这会影响网页的沉浸式体验，特别是对于全屏展示的摄影、艺术类网站。这个导航栏会占用约10%的屏幕空间，且无法通过常规的全屏API完全隐藏。

## 解决方案概述

本项目通过组合使用以下几种技术手段，成功实现了微信内置浏览器中的"真全屏"效果：

1. **CSS布局固定** - 使用固定定位和视口单位
2. **微信JS-SDK接口** - 调用微信提供的JS桥接口
3. **单页面应用设计** - 避免页面跳转导致导航栏重新出现
4. **事件监听和重复调用** - 确保各种状态下都能维持全屏效果

## 详细实现步骤

### 1. 基础CSS设置

首先，需要设置正确的CSS样式来固定页面布局：

```css
html, body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100vh; /* 使用视口高度 */
    overflow: hidden;
    background-color: #000; /* 添加黑色背景，避免白色闪烁 */
    /* 修复微信浏览器底部白线问题 */
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
}
```

关键点：
- 使用`position: fixed`固定整个页面
- 设置`height: 100vh`确保使用整个视口高度
- 禁用`overflow`防止滚动
- 设置四个方向的定位值为0，确保覆盖整个屏幕

### 2. 微信元标签设置

在HTML的head部分添加微信相关的元标签：

```html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<meta name="format-detection" content="telephone=no">
<meta name="x5-fullscreen" content="true">
<meta name="x5-page-mode" content="app">
```

这些元标签会告诉微信浏览器尽可能以全屏应用的方式显示内容。

### 3. 调用微信JS桥接口

使用微信提供的JS桥接口隐藏工具栏和选项菜单：

```javascript
function hideWechatNavigationBar() {
    if (typeof WeixinJSBridge !== 'undefined') {
        try {
            WeixinJSBridge.call('hideToolbar');
            WeixinJSBridge.call('hideOptionMenu');
        } catch (e) {
            console.error('隐藏微信导航栏失败:', e);
        }
    }
}

// 当微信JS桥准备就绪时隐藏导航栏
document.addEventListener('WeixinJSBridgeReady', function() {
    hideWechatNavigationBar();
}, false);
```

### 4. 监听关键事件

为确保在各种情况下都能维持全屏效果，需要监听多个事件：

```javascript
// 页面可见性变化时确保全屏
document.addEventListener('visibilitychange', function() {
    if (!document.hidden) {
        setTimeout(hideWechatNavigationBar, 100);
        setTimeout(hideWechatNavigationBar, 500);
    }
});

// 窗口大小变化时
window.addEventListener('resize', function() {
    document.documentElement.style.height = window.innerHeight + 'px';
    document.body.style.height = window.innerHeight + 'px';
    hideWechatNavigationBar();
});

// 阻止页面滚动和弹性效果
document.body.addEventListener('touchmove', function(e) {
    e.preventDefault();
}, { passive: false });
```

### 5. 避免页面跳转

微信浏览器在页面跳转后会重新显示导航栏。为避免这个问题，我们采用单页面应用设计，使用以下方法之一：

#### 方案A：使用iframe加载子页面

```html
<iframe id="content-frame" src="welcome.html"></iframe>

<script>
    // 监听来自iframe内部的消息
    window.addEventListener('message', function(event) {
        if (event.data && event.data.type === 'navigate') {
            document.getElementById('content-frame').src = event.data.target;
            // 确保下一页也会隐藏微信导航栏
            hideWechatNavigationBar();
        }
    });
</script>
```

#### 方案B：使用CSS显示/隐藏不同内容（推荐）

这是更可靠的方案，完全避免页面跳转：

```html
<div id="scene-welcome" class="scene active"><!-- 欢迎页内容 --></div>
<div id="scene-gallery" class="scene"><!-- 画廊内容 --></div>
<div id="scene-story" class="scene"><!-- 故事内容 --></div>

<script>
    function switchScene(sceneId) {
        // 隐藏所有场景
        document.querySelectorAll('.scene').forEach(scene => {
            scene.classList.remove('active');
        });
        // 显示目标场景
        document.getElementById(sceneId).classList.add('active');
        // 确保全屏效果
        hideWechatNavigationBar();
    }
</script>
```

对应的CSS：

```css
.scene {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: none;
}
.scene.active {
    display: block;
}
```

### 6. 定期检查和重复调用

为确保全屏效果的稳定性，可以设置定时器定期检查：

```javascript
// 定时重复调用以确保全屏
setInterval(hideWechatNavigationBar, 2000);
```

## 项目实际应用

在本项目中，我们将这些技术整合到了`menu.js`文件中，提供了一套完整的解决方案：

```javascript
// 初始化微信全屏
function initWechatFullscreen() {
    // 修复100vh高度问题
    document.documentElement.style.height = window.innerHeight + 'px';
    document.body.style.height = window.innerHeight + 'px';
    
    // 微信JS桥接就绪时
    document.addEventListener('WeixinJSBridgeReady', function() {
        hideWechatNavigationBar();
    }, false);
    
    // 页面可见性变化时
    document.addEventListener('visibilitychange', function() {
        if (!document.hidden) {
            setTimeout(hideWechatNavigationBar, 100);
            setTimeout(hideWechatNavigationBar, 500);
            setTimeout(hideWechatNavigationBar, 1000);
        }
    });
    
    // 窗口大小变化时
    window.addEventListener('resize', function() {
        document.documentElement.style.height = window.innerHeight + 'px';
        document.body.style.height = window.innerHeight + 'px';
        hideWechatNavigationBar();
    });
    
    // 阻止页面滚动和弹性效果
    document.body.addEventListener('touchmove', function(e) {
        if (e.target.classList && e.target.classList.contains('scrollable')) {
            // 允许特定元素滚动
            return;
        }
        e.preventDefault();
    }, { passive: false });
    
    // 定时重复调用以确保全屏
    setInterval(hideWechatNavigationBar, 2000);
}
```

## 注意事项和最佳实践

1. **避免页面跳转**：每次页面跳转都会导致微信导航栏重新出现，因此尽量使用单页面应用设计
   
2. **处理滚动内容**：如果需要在页面中包含可滚动内容，可以为特定元素添加`scrollable`类，并在阻止滚动的事件监听器中添加例外：
   ```javascript
   document.body.addEventListener('touchmove', function(e) {
       if (e.target.classList && e.target.classList.contains('scrollable')) {
           return; // 允许这个元素滚动
       }
       e.preventDefault();
   }, { passive: false });
   ```

3. **多次调用隐藏函数**：在关键时刻（页面加载、可见性变化等）多次调用隐藏函数，使用不同的延迟时间，以确保效果

4. **兼容性考虑**：这些方法主要针对微信内置浏览器，在其他浏览器中可能不需要或不起作用，可以添加微信环境检测

5. **动态内容加载**：如果需要动态加载内容，使用AJAX或Fetch API获取内容并更新DOM，而不是跳转到新页面

## 总结

通过组合使用CSS固定布局、微信JS桥接口、单页面应用设计和多重事件监听，我们成功实现了微信内置浏览器中的"真全屏"效果，为用户提供沉浸式的浏览体验。

这套解决方案已在"旅途摄影剧场"项目中成功应用，可以作为未来类似项目的参考模板。

## 参考资源

- [微信JS-SDK文档](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)
- [微信网页开发最佳实践](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Web_Developer_Tools.html) 