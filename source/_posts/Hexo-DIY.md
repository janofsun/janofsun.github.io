---
title: Hexo DIY
date: 2023-09-17 10:36:46
tags: Tools
categories: Hexo
---
<i class="fa-solid fa-cog fa-spin"></i>  Adding customized features
<!--more-->
#### **Pin/Unpin a post**
Pin or unpin a post in any order by modifying the source code:
- Open the "node_modules\hexo-generator-index\lib", then modify the sorting function in "generator.js";
- Pin or unpin a post by setting the "top" variable.

```javascript
module.exports = function(locals) {
  const config = this.config;
  const posts = locals.posts.sort(config.index_generator.order_by);

  // posts.data.sort((a, b) => (b.sticky || 0) - (a.sticky || 0));
  posts.data = posts.data.sort(function(a, b) {
    if (a.top && b.top) {
      if (a.top == b.top)
        return b.date - a.date;
      else 
        return b.top - a.top;
    }
    else if(a.top && !b.top) {
      return -1;
    } else if(!a.top && b.top) {
      return 1;
    } else return b.date - a.date;
  })
```

#### **Edit the layout**
Please Edit the style files (base.styl and responsive.styl) under the path "node_modules\hexo-theme-icarus\include\style".
- [Check the issue](https://github.com/ppoffice/hexo-theme-icarus/issues/658)
If you want to make the home page as 3-column and post pages as 2-column layout, please configure a new '_config.post.yml' file and modify the widescreen layout accordingly.
- [Adjust 2-column / 3-column layout and width](https://blog.csdn.net/ye17186/article/details/111564883)

#### **Rendering mathematical formulas**
Some symbols in the formula are being escaped by Hexo's Markdown processor, so the formula received by MathJax is incorrect. To solve this problem, you can use the {% raw %} code block to wrap the problematic formula.
- [Check this issue](https://github.com/ppoffice/hexo-theme-icarus/issues/394)

#### Add Night theme
- [Hexo 主题 icarus 4 配置夜间模式 Night Mode](https://blog.asahih.com/hexo-icarus-night-mode/)
- [hexo-theme-icarus 3.0.0 添加夜间模式 - sbx0's blog](https://github.com/sbx0/sbx0.github.io/issues/30)

#### Icons for Markdown
- [Font Awesome](https://fontawesome.com/docs/web/add-icons/how-to)
- [巧用 Font Awesome 装点 Markdown 文档](https://sspai.com/post/45217)

<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.0.13/css/all.css">