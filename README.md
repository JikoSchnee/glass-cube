# Glass Cube

一个使用原生 WebGL2 实现的交互式玻璃方块。页面不依赖第三方库或构建工具，所有代码都在 `index.html` 中。

## 页面与跳转

- 在线预览：[打开 Glass Cube](https://jikoschnee.github.io/glass-cube/)
- 源码仓库：[GitHub · JikoSchnee/glass-cube](https://github.com/JikoSchnee/glass-cube)
- 在预览页面点击右上角的 GitHub 图标，会在当前标签页打开源码仓库。
- 本地预览：克隆仓库后，直接在支持 WebGL2 的浏览器中打开 `index.html`。

```bash
git clone https://github.com/JikoSchnee/glass-cube.git
cd glass-cube
```

GitHub Pages 从 `main` 分支根目录发布 `index.html`。推送到 `main` 后，GitHub 会自动重新构建页面，更新可能需要一点时间。

## 交互

- 方块会自动旋转，鼠标移动会产生轻微视差。
- 拖拽页面可以旋转方块；滚动鼠标滚轮可以缩放。
- 代码顶部的 `CUBE.size` 控制方块半边长，`CUBE.spin` 控制自动旋转速度。

## 实现原理

页面先用二维 Canvas 绘制文字和线条，再将其上传为 WebGL 纹理，作为方块后面的背景。窗口尺寸变化时，背景和 WebGL 画布会一起更新。

玻璃方块并不是由三角形网格建模：片元着色器为每个屏幕像素生成一条视线，将视线变换到方块局部坐标系，并通过射线与轴对齐立方体的相交计算确定方块的表面和法线。未击中方块的像素直接采样背景纹理。

击中方块时，着色器分别为红、绿、蓝三个通道使用略有差异的折射率，追踪光线进入和离开方块后的方向，再沿出射方向采样背景纹理，形成文字扭曲与色散。光线在内部发生全反射时会继续反射，最多尝试三次出射。菲涅耳反射、边缘明暗、虹彩和高光共同塑造玻璃质感；双采样抗锯齿和少量颗粒感用于完善画面。

JavaScript 通过 `requestAnimationFrame` 更新旋转角度，将方块位置、大小、旋转矩阵和时间传给着色器。拖拽和滚轮改变旋转或缩放参数，鼠标位置则经过平滑处理后参与视差计算。

如需更换折射背景，可修改 `drawBackground()` 中的二维 Canvas 绘制内容。折射采样的是这张纹理，因此页面上另行叠加的普通 HTML 元素不会自动出现在玻璃折射中。
