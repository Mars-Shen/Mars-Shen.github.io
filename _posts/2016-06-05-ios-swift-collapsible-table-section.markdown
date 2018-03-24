---
layout:     post
title:      "如何在IOS中实现可折叠/展开的Table Section"
subtitle:   "使用swift2.2在IOS中实现可折叠/展开的Table Section"
date:       2016-06-06 12:00:00
author:     "Mars Shen"
header-img: "img/Header/coder-doom-dos-code-hd.jpg"
catelog: true
tags: [原创,翻译,IOS开发,swift]
---

<blockquote>原文链接以及源代码:<a href="https://github.com/jeantimex/ios-swift-collapsible-table-section">原文地址</a><br/>原文作者:Yong Su @ Box Inc.</blockquote>
<h2 class="section-heading">如何在IOS中实现可折叠/展开的Table Section?</h2>
<img src="https://img.shields.io/badge/Swift-2.2-orange.svg?style=flat" width="60">
<img src="{{ site.baseurl }}/img/IOSection/demo.gif" width="60%" heigh="60%">
<p><b>第一步:准备数据</b></p>
<p>
假设我们有下面这些分好组的数据,每一组是一个 <code>Section</code> 对象
{% gist Mars-Shen/7b1193a9ba6712e27ff0cf2c456b4896 %}
<code>collapsed</code> 表明了现在是否是折叠状态,默认为false.
</p>
<p><b>第二步:设计 Header 和 Cell</b></p>
<p>在story board中选择 <code>Table View</code>, 选择 <code>Dynamic Prototypes</code>并且设置 <code>Prototype Cells</code> 的值为 2, 一个给我们的自定义 Header,另外一个给我们每一行的 Cell, 然后将各自的 <code>Identifier</code> 设置为为 header 和 cell.</p>
<img src="{{ site.baseurl }}/img/IOSection/cell.png" width="60%" heigh="60%">
<p>添加一个 UIButton (用做切换折叠状态) 和一个 Label 到 header 的 prototype cell 中, 创建一个继承自 <code>UITableViewCell</code> 的 swift 文件并且命名为<code> CollapsibleTableViewHeader.swift</code>. 这个文件很简单, 有两个 IBOutlets, 一个用做链接切换按钮 (UIButton), 另外一个用做链接 Label. 最后我们设置这个 header 的 Class 为 我们自定义的类 <code>CollapsibleTableViewHeader</code> 并且在类中正确的链接 IBOutlets.</p>
<p>现在这个文件应该看起来像这样:</p>
{% gist Mars-Shen/5090cd69661d71d3659fb05a8f8fde61 %}
<p>创建一个 prototype cell 和一个继承自 UITableViewCell 的类, 有这两点好处:</p>
<ul>
<li>我们能可视化的设计我们的 header</li>
<li>我们不需要像这样为了创建一个 Cell 而创建一个 nib 文件 然后在 tableView 中注册他使用:</li>
</ul>
{% gist Mars-Shen/3afdc49795819b56efb7b56d42f655bb %}
<p>个人而言, 我不喜欢在我的项目中有 nib 文件, 并且如果我们用了 <code>dequeueReusableHeaderFooterViewWithIdentifier</code>, 好像我们必须在那个 section 中最少拥有一行 row, 但这里我们需要 0 row!</p>
<p><b>第三步: UITableViewDelegate</b></p>
<p>首先,用 sections.count 正确的表示 section 的数量:</p>
{% gist Mars-Shen/4ef5c46a29a1a25b2370f256d2a06af9 %}
<p>对于那些每个 section 中的 row 的数量, 我们使用 <code>collapsed</code> 变量去控制它, 如果 collapsed 是 true, 我们就返回 0, 否则返回他真实 items 的数量:</p>
{% gist Mars-Shen/ef831055d7ad39e37bee3b008d49a953 %}
<p>我们使用 tableView 的 viewForHeaderInSection 方法去实现我们自定义的 header:</p>
{% gist Mars-Shen/e89393cdef2d02331bebc51025c58f6b %}

<p>注意我们为我们的接切换按钮 (UIButton) 注册了一个touch up inside 事件, 一旦这个按钮被按下, 它将会触发 toggleCollapse 方法.</p>
<p>最后, 我们的 row cell 看起来像这样:</p>
{% gist Mars-Shen/2cc5bf8c952f936ad377a0a88b53f3c0 %}
<p>这个是 toggleCollapse 方法:</p>

{% gist Mars-Shen/b975a3f521857f229b56a673317ca370 %}
<p>好啦,就是这么简单,如果你需要看源代码,请到顶部原文链接中,你可以在那个链接中找到具体的实现代码</p>

<p><b>更多的可折叠Demo</b></p>
<p>有时你可能要在grouped-style table中实现一些可折叠的cells, 我有一些单独的Demo在<a href="https://github.com/jeantimex/ios-swift-collapsible-table-section-in-grouped-section">链接</a>中. 他的实现和今天我们学的这个非常相似,只有一点点小不同,如果有兴趣可以去看下.</p>

<img src="{{ site.baseurl }}/img/IOSection/otherDemo.gif" width="60%" heigh="60%">