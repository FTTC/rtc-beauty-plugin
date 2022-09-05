## 功能描述

TRTC 可以通过插件，帮助开发者轻松实现基础美颜功能。用户可以调整美颜参数，实现自然的美颜效果。
也可以通过插件来对视频增加人像分割的处理，在通话过程中呈现背景虚化或者虚拟背景的效果。
[点击此处](https://web.sdk.qcloud.com/trtc/webrtc/demo/api-sample/improve-beauty.html) 体验效果。

| 浏览器            | 版本        |
|------------------|------------|
| Chrome           | 65+        |
| Firefox          | 70+        |
| Safari           | 12+        |
| Edge             | 80+        |
| 移动端浏览器       | 不支持      |
| 微信内嵌网页       | 不支持      |

**注意事项**

- 除了以上支持的平台，其他平台均不支持
- 美颜和人像分割的计算和渲染都是比较消耗性能的，因此如果使用者的设备性能不足以支撑运算，建议不开启美颜和人像分割的功能。
  - 2.6 GHz 六核Intel Core i7 16G 内存处理器的设备性能数据：
    - 480p 15帧的视频，使用美颜处理时，占用 CPU 比正常音视频通话要多 10% 左右。
    - 480p 15帧的视频，使用背景虚化处理时，占用 CPU 比正常音视频通话要多 25% 左右。
    - 480p 15帧的视频，使用虚拟背景处理时，占用 CPU 比正常音视频通话要多 30% 左右，如果自定义背景的分辨率过高的话，可能会导致性能消耗进一步增高。
    

## 接入攻略

### 前提

**如果您需要使用 `RTCBeautyPlugin` 的美颜能力时，请将 TRTC SDK 升级到 4.11.1 及以上版本。**

**如果您需要使用 `RTCBeautyPlugin` 的人像分割能力时，请将 TRTC SDK 升级到 4.11.10 及以上版本。**


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

如果您想关闭美颜效果，将三个参数都设置为 0 即可。

#### Params:

| Name   | Type      | Description     |
|--------|-----------|-----------------|
| beauty    | `number`   | 美颜度( 0 - 1 ，默认为 0.5 )     |
| brightness   | `number` | 明亮度( 0 - 1 ，默认为 0.5 )      |
| ruddy  | `number`  | 红润度( 0 - 1 ，默认为 0.5 )    |

```javascript
beautyPlugin.setBeautyParam({ beauty: 0.5, brightness: 0.5, ruddy: 0.5 });

// 如果您想关闭美颜效果，将三个参数都设置为 0 即可，如果要重新开启，设置任意一个参数 > 0 即可打开美颜能力。
beautyPlugin.setBeautyParam({ beauty: 0, brightness: 0, ruddy: 0 });
```

### (async) loadResources()
**功能：**  如果您需要使用人像分割功能，您可以在一开始就调用 loadResources() 方法，来加载人像分割的计算资源。
```javascript
const rtcBeautyPlugin = new RTCBeautyPlugin();

// 加载计算资源
await rtcBeautyPlugin.loadResources();
```


### (async) generateVirtualStream({ localStream, type, img })

将 localStream 处理成经过人像分割处理后的 beautyStream。

#### Params:

| Name        | Type          | Description                              |
|-------------|---------------|------------------------------------------|
| localStream | `LocalStream`   | 通过 TRTC.createStream 方法创建的本地流            |
| type        | `string`           | `blur` 为背景虚化，`virtual`为虚拟背景              |
| img   | `HTMLImageElement` | 用于在虚拟背景中生成背景图，建议图片分辨率不超过480p，避免计算时性能开销过大 |

```javascript
// 生成背景虚化的流
const rtcBeautyPlugin = new RTCBeautyPlugin();
await rtcBeautyPlugin.loadResources();
await localStream.initialize();

const virtualStream = await rtcBeautyPlugin.generateVirtualStream({ 
  localStream: localStream,
  type: 'blur'
});
await client.publish(virtualStream);
```

```javascript
// 生成虚拟背景的流
const rtcBeautyPlugin = new RTCBeautyPlugin();
await rtcBeautyPlugin.loadResources();
await localStream.initialize();

const virtualStream = await rtcBeautyPlugin.generateVirtualStream({
  localStream: localStream,
  type: 'virtual',
  img: document.getElementById('img'),
});
await client.publish(virtualStream);
```
###  (async) generateStream({ localStream, type, img })

生成既有人像分割处理，又有美颜功能的流

#### Params:

| Name        | Type          | Description                              |
|-------------|---------------|------------------------------------------|
| localStream | `LocalStream`   | 通过 TRTC.createStream 方法创建的本地流            |
| type        | `string`           | `blur` 为背景虚化，`virtual`为虚拟背景              |
| img   | `HTMLImageElement` | 用于在虚拟背景中生成背景图，建议图片分辨率不超过480p，避免计算时性能开销过大 |


```javascript
const rtcBeautyPlugin = new RTCBeautyPlugin();
await rtcBeautyPlugin.loadResources();
await localStream.initialize();
// 生成人像分割和美颜处理后的流
const stream = await rtcBeautyPlugin.generateStream({ 
  localStream: this.localStream_,	  
  type: 'virtual',	  
  img: document.getElementById('img'),
});
// 发布经过美颜后的流
await client.publish(stream);
```

### setBackground(img)
**功能：** 使用人像分割的自定义背景功能时用于动态改变背景图。

#### Params:

| Name | Type         | Description |
|------|--------------|-------------|
| img   | `HTMLImageElement` | 用于在虚拟背景中生成背景图，建议图片分辨率不超过480p，避免计算时性能开销过大   |

```javascript
beautyPlugin.setBackground(document.getElementById('newImg'));
```

### destroy()
**功能：** 销毁美颜插件。

**使用方式：** 在推流结束之后，可以销毁美颜插件，避免内存占用和性能消耗。

```javascript
await client.leave();
beautyPlugin.destroy();
```

## 常见问题
1. 一个 `RTCBeautyPlugin` 实例只能处理一条本地流。

2. 使用 `replaceTrack` 等操作会导致您的 `localStream` 美颜效果消失，请酌情使用。
