# 🍅 小番茄图片批量混淆

基于广义 Hilbert 空间填充曲线的图片批量混淆 / 解混淆工具。  
所有处理均在浏览器本地完成，无需上传服务器，适合技术交流与个人使用。

> 算法基于 [jiarandiana0307/PicEncrypt](https://github.com/jiarandiana0307/PicEncrypt)  
> 参考自 [singularpoint.cn/hideImg1.html](https://singularpoint.cn/hideImg1.html)  
> 本仓库为独立编写的 Web 批量版本。

## 在线体验

部署到 GitHub Pages 后访问：

```text
https://<your-username>.github.io/tomato-image-scramble-batch/
```

例如：

```text
https://yourname.github.io/tomato-image-scramble-batch/
```

## 功能特性

- 批量导入图片
  - 拖拽上传
  - 点击选择文件
  - 支持 `Ctrl + V` 粘贴剪贴板图片
- 支持压缩包导入
  - `zip`：使用浏览器原生 `DecompressionStream` 解析
  - `rar` / `7z`：通过 `libarchive.js` 的 WASM 解压器解析
- 自动提取压缩包中的图片并生成预览卡片
- 批量混淆
- 批量解混淆
- 一键还原为导入时的原图
- 单张图片下载
- 逐个下载全部图片
- 打包为 ZIP 下载
- 输出格式统一为 JPEG，质量 `1`
- 纯前端实现，无需后端服务

## 使用说明

1. 打开网页。
2. 将图片或压缩包拖入上传区域，或点击选择文件。
3. 也可以直接 `Ctrl + V` 粘贴剪贴板中的图片。
4. 等待图片列表和预览图生成。
5. 点击：
   - `批量混淆`：对所有图片执行混淆
   - `批量解混淆`：对所有图片执行解混淆
   - `还原全部`：恢复为导入时的原图
   - `打包 ZIP 下载`：将所有当前结果打包下载
   - `逐个下载`：依次下载所有当前结果
   - `清空列表`：移除全部图片
6. 单张卡片上也可以单独下载或移除。

## 技术说明

### 混淆算法

项目使用广义 Hilbert 空间填充曲线对图片像素进行重新排列。  
该曲线能够保持像素之间的空间相关性，因此混淆后的图片在被压缩后仍可能保留一定色彩结构。

核心流程：

1. 将图片绘制到 Canvas。
2. 读取 `ImageData`。
3. 使用 `gilbert2d(width, height)` 生成像素遍历顺序。
4. 根据偏移量进行像素位置置换：

```js
const offset = Math.round((Math.sqrt(5) - 1) / 2 * width * height);
```

5. 生成新的 `ImageData`。
6. 导出为 JPEG，质量 `1`。

### ZIP 解析

ZIP 读取与写入由浏览器原生 API 完成，不依赖外部库。  
ZIP 解压使用：

```js
DecompressionStream('deflate-raw')
```

因此需要较新的现代浏览器。

### RAR / 7Z 解析

RAR / 7Z 通过 CDN 动态加载 `libarchive.js`：

```text
https://cdn.jsdelivr.net/npm/libarchive.js@2.0.2/dist/
```

首次使用需要联网加载 WASM 解压组件。  
如果 CDN 不可访问，则 RAR / 7Z 解压会失败。

## 浏览器兼容性

推荐使用：

- Chrome 103+
- Edge 103+
- 其他支持 `DecompressionStream` 和 ES Module 的现代浏览器

注意：

- ZIP 的 deflate 解压依赖 `DecompressionStream`
- RAR / 7Z 解压依赖 `libarchive.js` 和 WASM
- 不支持 Zip64、加密 ZIP 等复杂格式
- 超大图片处理可能较慢，建议单张图片不要超过 4000 万像素

## 本地运行

方式一：直接双击 `index.html` 打开。

方式二：启动本地静态服务器：

```bash
python -m http.server 8000
```

然后访问：

```text
http://localhost:8000
```

## 部署到 GitHub Pages

1. 新建 GitHub 仓库，例如：

```text
tomato-image-scramble-batch
```

2. 将 `index.html` 上传到仓库根目录。
3. 进入仓库 `Settings` -> `Pages`。
4. `Source` 选择：

```text
Deploy from a branch
```

5. `Branch` 选择：

```text
main
/ (root)
```

6. 点击 `Save`。
7. 等待几分钟后访问：

```text
https://<your-username>.github.io/tomato-image-scramble-batch/
```

如果仓库名是：

```text
<your-username>.github.io
```

则访问：

```text
https://<your-username>.github.io/
```

## 目录结构

```text
.
├── index.html
└── README.md
```

如果 `index.html` 中引用了：

```html
<link rel="shortcut icon" type="image/x-icon" href="assets/images/favicon.svg" />
```

请同时添加：

```text
assets/images/favicon.svg
```

或者删除该行，避免 404。

## 注意事项

- 本项目仅供技术交流使用。
- 请勿用于非法用途。
- 输出为 JPEG，质量 `1`，但仍属于有损压缩。
- 混淆 / 解混淆不是加密，不能作为安全加密手段。
- 压缩包中的图片越多、尺寸越大，处理时间越长。
- RAR / 7Z 解压需要联网加载第三方解压组件。
- 当前 ZIP 实现不支持 Zip64 与加密 ZIP。

## 致谢

- [jiarandiana0307/PicEncrypt](https://github.com/jiarandiana0307/PicEncrypt)
- [singularpoint.cn/hideImg1.html](https://singularpoint.cn/hideImg1.html)
- [libarchive.js](https://github.com/nika-begiashvili/libarchivejs)

## License

仅供技术交流使用。  
请同时遵循原算法项目及相关依赖的许可要求。
