最近为博客字体的事糟透了心。在macOS上能正常显示的“娃娃体-简”字体，在IOS和Windows上却没能正常显示。虽然依旧没有完美解决这个问题，好在经过一番折腾，终于理出了一些头绪绕过了它。感谢葵中剑的这篇[文章](https://swordair.com/woff2-and-icon-font/)为我提供的帮助。为了理解web环境中字体的工作原理，在此先列出如下几点概念：
##### 1. 字体由平台提供
在不使用**font-face**添加自定义字体的前提下，web能提供给用户哪些字体取决于用户使用的平台支持哪些字体。以下列出几个平台查看支持哪些字体的方法：
macOS:
应用程序 -> 其他 -> 字体册；
windows:
a) win键+R -> 输入fonts 回车；
b) 控制面板 -> 外观和个性化 -> 字体；
IOS:
设置 -> 通用 -> 辅助功能 -> 媒体：字幕与隐藏式字幕 -> 样式 -> 创建新的样式 -> 字体 -> 高级；
Android:
安卓系统在中国已经被各大厂商深度定制，这里就不再一概而论得给出路径了。


*PS:在macOS和windos平台上我们可以导入或导出需要的字体文件(.otf)。*

##### 2. 网页常用的字体分为5类
- serif(衬线体)在字符笔画末端有叫做衬线的小细节，这些细节在大写字母中特别明显。
![](https://tabrisk.github.io/blog-articles/custom-font/%E8%A1%AC%E7%BA%BF%E4%BD%93.png)
- sans-serif(无衬线)字体在字符笔画末端没有任何细节，与serif字体相比，他们的外形更简单。
![](https://tabrisk.github.io/blog-articles/custom-font/无衬线.png)
- monospace(等宽)每个字母的宽度相等，通常用于计算机相关书籍中排版代码块。
![](https://tabrisk.github.io/blog-articles/custom-font/%E5%AE%BD%E4%BD%93.png)
- fantasy(梦幻)这类字体通畅会很花哨，或者在书写工具上做文章。它们在浏览器中不常用。
![](https://tabrisk.github.io/blog-articles/custom-font/梦幻.png)
- cuisive(草体)与fantasy类似，区别是这类字体应该是人能书写出的文字，且书写工具一般都是平滑流畅的笔。它在浏览器中也不常用。


*PS:对没有实现中文的字体，在遇到需要显示中文时将直接显示无衬线体中文*

##### 3. 几种主要的字体文件格式
(按其诞生时代排序)
- **.ttf**(True Type Fonts)80年代末被提出，在操作系统里非常常见，web中主要为了支持早期仅支持TTF和OTF的浏览器。文件较大，需要服务器额外压缩；
- **.otf**(Open Type Fonts)1996年发布，在TTF的基础上增加了若干新特性，且一改ttf点阵法构字的模式，采用矢量图构字；
- **.eot**(Embadded Open Type)主要用于早期版本的IE，是其专有格式，带有版权保护和压缩。2008年它被纳入CSS3标准的字体格式，并有机会成为W3C官方的推荐字体格式，但很遗憾最终W3C选择了WOFF；
- **.woff**(Web Open Font Format)可以看作是ttf的再封装，加入了压缩和字体来源信息，通常比ttf小40%。也是当前web字体的主流格式;
- **.woff2**(Web Open Font Format 2.0)字面意思，WOFF的2.0版，其压缩比在WOFF的基础上又提高了25%。
当下主流浏览器主要使用WOFF和WOFF2格式的字体，其兼容性可以参考MDN的font-face条目。

回过头来看遇到的问题。发现"娃娃体-简"字体仅在macOS上存在，因此之前在IOS和windows上无法显示。我从mac的字体册中导出了该字体文件(.otf)并作为自定义字体加入博客中。

```css
@font-face {
  font-family: 'Wawati client';
  src: url(../font/Wawati_SC/WawaSC-Regular.otf) format("opentype");
  font-weight: normal;
  font-style: normal;
}
...
ol {
            font-family: "Wawati SC", "Wawati client";
            padding-left: 17px;
    }
...
```
为什么我给它起名"Wawati client"呢？这里要提一点，浏览器为文本匹配字体的顺序是自左向右逐个匹配的。如果匹配不到左侧的字体，它才会去寻找下一个字体。而@font-face中的自定义字体只有在文本不得不使用它时，它才会去load字体文件到本地并应用。
这里浏览器会先去寻找"Wawati SC"字体，如果用户本地系统就存在该字体，则不会再去理会自定义字体"Wawati client"。当"Wawati SC"不存在时，浏览器触发load"Wawati client"字体文件的请求。
要知道，带中文的字体文件是相当大的！WawaSC-Regular.otf中包含了大部分的中文简体字，因此它足足有17MB，这显然是不允许的。通过在线的otf2woff、otf2woff2工具转换得到了压缩后的文件，其大小分别是9MB和6MB，依旧很大。只能更改设计啦。
```css
...
ol {
            font-family: "Wawati SC";
            padding-left: 17px;
    }
...
```
考虑手机端用户的流量问题，放弃使用自定义字体。系统存在"娃娃体-简"则直接应用，否则使用继承的"无衬线"体。