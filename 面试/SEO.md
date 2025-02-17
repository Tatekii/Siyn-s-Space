# SEO(Search Engine Optimization)
搜索引擎优化，提升网站在搜索结果中排名的技术。

## 优化
- 爬虫设置
	- `robot.txt`合理的allow和disallow
	- `<meta name="robots" content="index, follow">`
- 合理的文档结构h1,h2,h3
- 合理的meta标签
	- title
	- description
	- keywords
	- charset
	- author
	- copyright
```html
<title>2024 年最佳 SEO 优化技巧 - 提升网站排名</title>
<meta name="description" content="掌握 2024 年最新 SEO 技巧，提高网站排名，优化网站结构，关键词策略，提升流量。">
<meta name="keywords" content="SEO, 网站优化, 提高排名, 关键词, Google SEO">
```
- viewport
	- 移动端SEO
```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```
- 社交媒体优化（Open Graph & Twitter Cards）
```html
<meta property="og:title" content="2024 年最佳 SEO 优化技巧 - 提升网站排名">
<meta property="og:description" content="掌握 2024 年最新 SEO 技巧，提高网站排名，优化网站结构，关键词策略，提升流量。">
<meta property="og:image" content="https://example.com/seo-guide.jpg">
<meta property="og:url" content="https://example.com/seo-guide">	
<meta property="og:type" content="article">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="2024 年最佳 SEO 优化技巧">
<meta name="twitter:description" content="掌握最新 SEO 技巧，提高网站排名和流量。">
<meta name="twitter:image" content="https://example.com/seo-guide.jpg">
```
- 结构化数据`JSON-LD`
	- 帮助搜索引擎理解页面内容，提高 **富结果（Rich Snippets）** 展示概率
```html
<script type="application/ld+json">

{

  "@context": "https://schema.org",

  "@type": "Article",

  "headline": "2024 年最佳 SEO 优化技巧",

  "author": {

    "@type": "Person",

    "name": "张三"

  },

  "datePublished": "2024-02-20",

  "image": "https://example.com/seo-guide.jpg",

  "url": "https://example.com/seo-guide"

}

</script>
```
- 站点地图sitemap
	- 提交给引擎的XML sitemap
	- 给用户的html sitemap
- 网站导航
	- nav标签
- 高权重外链，友情链接
- 如果是SPA应用，迁移到SSR
