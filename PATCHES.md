# 本地改动说明（基于上游 1.0.1）

分支：`main`（由 `refactor/hls-mini` 快进合并）
基线提交：官方 1.0.1（`08679d0`）
保存版本：`v1.0.1-hls-mini.1`

## 功能

### 1. HLS 直播支持
- 仅 FLV 的直播间（如 719260）会回退到 HLS（fmp4）
- 宿主：`pickHlsStream()`、`/api/hls/*` 代理、`/api/hls-open` 短 token、`/vendor/hls.js`
- 客户端：`loadHls()` + 播放器 HLS 分支（`MANIFEST_PARSED` / `canplay` / `timeupdate` 同步 UI）

### 2. 独立迷你小窗
- 播放器控制条 `⧉` 打开；页面暂停（单路流），小窗用独立 video + 播放器
- 可拖动；顶部栏：弹幕开关 / 设置 / 尺寸 / 回页面 / 暂停 / 关闭
- 尺寸 0.85x / 1x / 1.3x / 1.6x（记忆）
- 画面按视频比例铺满，可裁边 0~30%
- 顶部栏鼠标静止 3 秒自动淡出

### 3. 小窗弹幕
- 通道：页面弹幕 hook 广播 + 小窗 1s 轮询 `/api/danmu/recent`（宿主房间缓冲）
- 设置：速度 / 显示区域 / 不透明度 / 字号，存 localStorage
- 调试：设置面板「显示调试信息」可显示轮询计数与「测试」按钮

### 4. 回页面
- 直播页内：复用现有播放器续播（取消静音 + play），不重建
- 其他页面：自动 `selectPanel('bilibili-multiview')`，页面挂载时以已解锁状态起播
- 播放器实例失效时才重建
- 视频实际播放后自动清除 `needsTap` 遮罩

## 本地配置文件位置
- 小窗弹幕设置：`localStorage['bili-mini-dm-cfg']`
- 小窗尺寸：`localStorage['bili-mini-size']`
- 裁边：`localStorage['bili-mini-cut']`
- 调试开关：`localStorage['bili-mini-debug']`

## 注意
- 首次进入直播页仍需点一次「开启声音」（浏览器自动播放策略）
- 小窗与页面播放器相互独立，切换有 0.5~2 秒重新缓冲

### 5. 自动播放兜底（刷新/换源）
- 重建播放器后若带声播放被浏览器拦截：自动静音起播、隐藏「点击继续播放」遮罩
- 页面任意一次点击自动取消静音，无需专门按钮
