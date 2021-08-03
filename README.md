## 功能描述

TRTC 可以通过插件，帮助开发者轻松实现基础美颜功能。用户可以调整美颜参数，实现自然的美颜效果。[点击此处](https://web.sdk.qcloud.com/trtc/webrtc/test/latest/beauty/index.html) 体验美颜效果。

| 浏览器            | 版本        |
|------------------|------------|
| Chrome           | 65+        |
| Firefox          | 70+        |
| Safari           | 12+        |
| Edge             | 80+        |
| 移动端浏览器       | 不支持      |
| 微信内嵌网页       | 不支持      |

## 接入攻略

### 前提

使用 `RTCBeautyPlugin` 时，请将 TRTC SDK 升级到 4.11.1 及以上版本。

在项目中安装 [RTCBeautyPlugin](https://www.npmjs.com/package/rtc-beauty-plugin) 插件。

```shell
npm install rtc-beauty-plugin
```

### Step1. 创建 RTCBeautyPlugin 实例
一个 RTCBeautyPlugin 实例只能用来处理一条本地音视频流。

```javascript
const rtcBeautyPlugin = new RTCBeautyPlugin();
```

### Step2. 使用 RTCBeautyPlugin 的实例处理需要发布的流

```javascript
const beautyStream = rtcBeautyPlugin.generateBeautyStream(localStream);

// 发布经过美颜后的流
await client.publish(beautyStream);
```

## API 说明

### generateBeautyStream(localStream)

将 localStream 处理成经过美颜后的 beautyStream。

```javascript
// 初始化美颜插件
const rtcBeautyPlugin = new RTCBeautyPlugin();

await localStream.initialize();
// 生成美颜处理后的流
const beautyStream = rtcBeautyPlugin.generateBeautyStream(localStream);
// 发布经过美颜后的流
await client.publish(beautyStream);
```

### setBeautyParam(options)
**功能：** 调节美颜插件的美颜程度。

#### Params:

| Name   | Type      | Description     |
|--------|-----------|-----------------|
| beauty    | `number`   | 美颜度( 0 - 1 ，默认为 0.5 )     |
| brightness   | `number` | 明亮度( 0 - 1 ，默认为 0.5 )      |
| ruddy  | `number`  | 红润度( 0 - 1 ，默认为 0.5 )    |

```javascript
beautyPlugin.setBeautyParam({ beauty: 0.5, brightness: 0.5, ruddy: 0.5 });
```

### destory()
**功能：** 销毁美颜插件。

**使用方式：** 在推流结束之后，可以销毁美颜插件，避免内存占用和性能消耗。

```javascript
await client.leave();
beautyPlugin.destroy();
```

## 常见问题
1. 一个 `RTCBeautyPlugin` 实例只能处理一条本地流。

2. 使用 `replaceTrack` 等操作会导致您的 `localStream` 美颜效果消失，请酌情使用。
