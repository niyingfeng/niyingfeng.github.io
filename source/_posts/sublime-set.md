---
title: sublime 前端配置详解
date: 2016-05-29
tags: sublime
---
\前两天 sublime 出了莫名其妙的BUG，只得重新安装，可谓是惨不忍睹啊。一切屌屌的配置都要从新配起。虽说网上一大堆文献，可惜天下文章一大抄，夸张的说，如果某一篇文章出现文章，整个关键词的文章就废掉了。。。一怒之下，自己整理一篇小记，废话不多说，装起先。
### package control
一般 sublime 插件都需要基于 package control，所以首先需要先安装 package control。 两种方法： `Ctrl + \` 或者 View - Show Console 打开 sublime 的控制台，网上好多同一版本的链接都已经失效，导致无法安装。这边提供最新有效的控制台安装代码。` `sublime 3 输入：`

```
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

`sublime 2 输入：`

```
import urllib2,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllibinstall_opener( urllibbuild_opener( urllibProxyHandler()) ); by = urlliburlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

`若安装不成功，则先下载 https://github.com/lovenyf/file/blob/master/Package%20Control.sublime-package 将文件放置于 Preferences - Browse Packages 上一级目录的 Installed Packages 中，就ok了。` 然后是一大波前端实用的 sublime 插件（均可以在 package control 下安装）

*   HTMLBeautify，CSS Format，JS Format 代码规范格式化插件，功能虽小，但是相当实用。
*   Git 如果你们是基于 git 开发环境的，sublime 的这个小玩意也会让你爱不释手，无需切回 shell控制台来执行一些 git 命令了。
*   Git Gutter 非常爽的玩意，能在编辑器内看到diff的内容等
*   Emmet 无需多说，极速编写html与css， Ctrl + e 搞定一切。（http://emmet.io/）
*   SublimeCodeIntel 代码提示插件，支持多种编程语言。（https://github.com/SublimeCodeIntel/SublimeCodeIntel）
*   CssComb 将杂乱无章的 css 规范 近似一类的样式 归结在一起。
*   MarkdownEditing 如果没有使用过Markdown，建议可以去使用使用。 MarkdownEditing是一个Sublime Text的Markdown插件。提供了一种合适的Markdown着色方案(light 与 dark)，包含强大的语法高亮，和实用的Markdown编辑功能。支持3种风格：Standard Markdown, GitHub flavored Markdown, MultiMarkdown。
*   SASS Bulid SASS Build 是一个编写CSS的预处理器。这个特别的插件将帮助你妥善构建包括压缩选项在内的SASS文件。一旦你安装了这个插件
*   SideBarEnhancements 侧栏右键菜单增强插件，添加大量实用功能。
*   DocBlockr ( https://github.com/spadgos/sublime-jsdocs ) , 规范代码注释，编写可维护的代码。