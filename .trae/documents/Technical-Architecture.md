# 技术架构文档

## 技术栈

- **HTML5**：单文件结构，语义化标签。
- **CSS3**：自定义属性、渐变、滤镜、动画、变换、响应式媒体查询。
- **原生 JavaScript (ES6+)**：模块化函数、Web Animations API、Canvas 2D、Fetch API。
- **第三方 CDN**：
  - hls.js：M3U8 流媒体播放与降级。
  - Google Fonts：Cormorant Garamond / Playfair Display / Inter / Montserrat。

## 模块划分

### 1. 配置层
- `WEATHER_API_KEY`：OpenWeatherMap API 密钥占位符。
- `CHANNELS`：频道对象数组 `{ name, url }`。
- `DEFAULT_CITY`：默认城市（北京）。

### 2. 背景层
- **Canvas 粒子系统**：`ParticleNetwork` 类，管理粒子更新、连线、天气/时间响应。
- **极光层**：CSS 渐变动画 + JS 随机触发漂移。
- **鼠标聚光灯**：监听 `mousemove`，通过 CSS 自定义属性 `--x`, `--y` 控制 radial-gradient。

### 3. 天气模块
- `WeatherService`：封装 OpenWeatherMap API 请求（当前天气 + 3 日预报）。
- `WeatherRenderer`：根据天气 ID 切换动态矢量场景（SVG/CSS/Canvas 内嵌）。
- `WeatherCardUI`：更新 DOM 数据、温度发光文字、湿度/风力进度条。

### 4. 直播模块
- `LivePlayer`：初始化 hls.js 或原生 HLS，处理加载、播放、暂停、音量。
- `ChannelController`：频道切换、转场动画、粒子反馈、底部栏状态。
- `PlayerControls`：悬浮控制条交互。

### 5. 时间与品牌
- `TimeOrb`：右下角水晶球，实时更新时间与日期。
- `Logo`：左上角雕刻感 Logo 与金色扫描线。

### 6. 响应式适配
- 媒体查询切换布局为上下堆叠。
- 移动端减少粒子数量，频道栏改为横向滚动。

## 数据流

```
配置层 -> 各模块初始化
用户交互 -> 模块状态更新 -> DOM/Canvas 渲染
API 响应 -> 天气状态 -> 背景粒子/天气动画/文字更新
```

## 性能考虑

- 使用 `requestAnimationFrame` 驱动 Canvas 动画。
- 根据屏幕尺寸动态调整粒子数量。
- 暂停不可见标签页的动画（`visibilitychange`）。
- 直播切换时销毁旧 HLS 实例，避免内存泄漏。

## 错误处理

- 天气 API 失败：卡片显示优雅错误状态，保留直播。
- 直播源失败：显示源不可用提示，允许继续切换。
- 网络超时与无效城市名统一处理。

## 文件结构

```
/workspace/
├── index.html          # 完整单文件产物
├── .trae/documents/
│   ├── PRD.md
│   └── Technical-Architecture.md
└── 工具.html           # 已有文件，保留
```
