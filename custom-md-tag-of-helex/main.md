# 初衷

Markdown 本身没有提供嵌入网页的相关语法。当文章作者希望直接嵌入相关网页给用户预览、操作时，用户必须通过一个链接跳转或新开一个页面来达到目的。为了在本博客中直观的让用户使用网页，现加入一种自定义的语法。

# 示例

在 Markdown 中插入如下内容

\`\`\`iframe<br>
ht<span>tps://ww</span>w.baidu.com<br>
\`\`\`

即可在本博客渲染出目标页面

```iframe
https://www.baidu.com
```

# 实现

实现原理参考 marked [扩展功能文档](https://marked.js.org/#/USING_PRO.md)。此处对 renderer 的 code 进行了功能扩展。

为什么选择 code 进行扩展？因为 code 是用来优化代码展示的，而将作品链接优化到文章中直接展示并没有超出优化代码展示的含义（作品本身也是代码堆砌而成的），所以此处选择对 code 而不是其他语法关键字进行扩展。