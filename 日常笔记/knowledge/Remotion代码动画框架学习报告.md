# Remotion 代码动画框架学习报告

Remotion 是用 React 来创建视频的框架。核心想法：给你当前帧号和空白画布，用 React 渲染这一帧。

> 相关阅读：[[现代编程语言学习笔记#七、JavaScript/TypeScript 运行时]]

---

## 安装

### 前置要求

Node.js 16+ 或 Bun 1.0.3+

### 创建新项目

| Package Manager | Command |
| --- | --- |
| npm | `npx create-video@latest` |
| pnpm | `pnpm create video` |
| yarn | `yarn create video` |
| bun | `bun create video` |

首次使用建议选 Hello World 模板。

---

## 核心原理

Remotion 的核心：给你当前帧号和空白画布，用 React 渲染这一帧。

### 视频四个必定义属性

width：像素宽度。height：像素高度。durationInFrames：总帧数。fps：帧率。

第一帧是 0，最后一帧是 durationInFrames - 1。

### 核心 API

| API | 作用 |
| --- | --- |
| useCurrentFrame() | 返回当前帧号 |
| useVideoConfig() | 返回 {fps, width, height, durationInFrames} |
| AbsoluteFill | 填满整个画布的容器 |

### 注册 Composition

在 `src/Root.tsx` 中用 Composition 把 React 组件注册成可渲染的视频：

```tsx
<Composition
  id="MyVideo"
  component={MyVideo}
  durationInFrames={150}
  fps={30}
  width={1920}
  height={1080}
/>
```

多个 Composition 用 Fragment 包裹：

```tsx
<>
  <Composition id="Video1" ... />
  <Composition id="Video2" ... />
</>
```

---

## 动画属性

### 基本原理：帧号驱动属性值

```tsx
import {AbsoluteFill, useCurrentFrame} from "remotion";

export const FadeIn = () => {
  const frame = useCurrentFrame();
  const opacity = Math.min(1, frame / 60);

  return (
    <AbsoluteFill style={{justifyContent: "center", alignItems: "center"}}>
      <div style={{opacity}}>Hello World!</div>
    </AbsoluteFill>
  );
};
```

### interpolate() 区间映射

```tsx
import {useCurrentFrame, interpolate} from "remotion";

const frame = useCurrentFrame();
const opacity = interpolate(frame, [0, 60], [0, 1], {
  extrapolateRight: "clamp",
});
```

逻辑：输入 [0, 60] 映射到输出 [0, 1]。超过 60 帧后 clamp 保持为 1。

### spring() 弹簧动画

```tsx
import {spring, useCurrentFrame, useVideoConfig} from "remotion";

export const MyVideo = () => {
  const frame = useCurrentFrame();
  const {fps} = useVideoConfig();

  const scale = spring({fps, frame});  // 默认 0→1，带回弹

  return (
    <div style={{transform: `scale(${scale})`}}>Hello World!</div>
  );
};
```

必须传 fps，因为弹簧模拟依赖时间单位。

### 关键规则

不要用 CSS transition/keyframes，否则渲染时会闪烁。所有动画必须由 useCurrentFrame() 驱动。

---

## 可复用性

### Sequence 时间编排

控制组件在哪段时间显示，并偏移子组件的 useCurrentFrame()：

```tsx
const MyTrailer = () => {
  return (
    <>
      <Sequence durationInFrames={30}>
        <Intro />              {/* 0-29帧 */}
      </Sequence>
      <Sequence from={30} durationInFrames={30}>
        <Clip />               {/* 30-59帧 */}
      </Sequence>
      <Sequence from={60}>
        <Outro />              {/* 60帧到结束 */}
      </Sequence>
    </>
  );
};
```

子组件的帧号被偏移：Clip 在实际第 30 帧时，内部 useCurrentFrame() 返回 0。Clip 在实际第 59 帧时，内部 useCurrentFrame() 返回 29。

### 嵌套时偏移累加

```tsx
<Sequence from={30}>
  <Sequence from={-15}>
    <BlueSquare />   {/* 实际从第15帧开始 */}
  </Sequence>
</Sequence>
```

### Sequence 属性

| 属性 | 说明 | 默认值 |
| --- | --- | --- |
| from | 开始帧 | 0 |
| durationInFrames | 持续帧数 | Infinity |
| name | Studio 时间轴标签 | - |
| layout | "absolute-fill" 或 "none" | "absolute-fill" |

---

## 预览

启动 Remotion Studio 预览视频：

Regular templates:

```bash
npm run dev
```

Next.js + React Router 7 templates:

```bash
npm run remotion
```

这是 `npx remotion studio` 的简写。服务启动在端口 3000（不可用则用更高端口），浏览器自动打开 Studio。

旧项目用 `npm start`，现在改为 `npm run dev`。

> 相关阅读：[[现代编程语言学习笔记]]