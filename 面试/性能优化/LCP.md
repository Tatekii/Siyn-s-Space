## 🚀 LCP 是什么

LCP 衡量的是**页面主内容（最大可见内容）出现在视口中的时间**，比如 Hero 图、大标题、视频等。Google 建议：
- **优秀**：≤ 2.5 秒
- **需改进**：2.5s ~ 4.0s
- **差**：> 4.0 秒
## ⚙️ 优化核心方向
|**优化点**|**说明**|
|---|---|
|🖼️ **优化首屏图片加载**|LCP 元素往往是图片，如 Banner 图，需优先加载|
|🛜 **减少资源阻塞**|避免 render-blocking 资源，如未压缩的 CSS、字体|
|🚫 **避免 CLS（布局偏移）**|布局不稳定会延迟 LCP 出现|
|⚡ **加快服务器响应时间**|TTFB 高会拖慢 LCP 时间|
|🔍 **合理使用懒加载与预加载**|首屏内容禁用 lazy，非首屏启用 lazy|
## 优化策略
### **图片优化**
- ✅ 使用 next/image（Next.js）自动处理懒加载、尺寸、格式
- ✅ 选择现代格式：WebP, AVIF
- ✅ 使用 priority 标记首屏图像（Next.js 示例）
	```tsx
	<Image src="/hero.jpg" alt="Hero" priority width={800} height={400} />
	```
- ✅ 设置尺寸避免 CLS：
```html
<img src="..." width="800" height="400" />
```

### 字体优化
- ✅ 使用 font-display: swap（防止字体加载阻塞渲染，字体加载完后切换）
- ✅ 使用自托管字体
- ✅ 限制字符集范围

### 脚本优化
- ✅ 移除未使用的 CSS（Tree Shaking）
- ✅  CSS压缩（Minify CSS）
	- 移除空格与换行
	- 移除注释
	- 简化颜色值
	- 合并重复属性规则
- ✅ 延迟加载非关键 JS（dynamic import() + suspense）
- ✅ 使用 CSS Modules 或 Tailwind，避免大型全局样式表
### 服务器与 CDN 优化
- ✅ 使用CDN
- ✅ 启用 HTTP/2 or HTTP/3
- ✅ 使用 SSR/SSG 提前渲染页面内容