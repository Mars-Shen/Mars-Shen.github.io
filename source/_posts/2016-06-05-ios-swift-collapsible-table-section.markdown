---
layout: post
title: 如何在IOS中实现可折叠/展开的Table Section
subtitle: 使用swift2.2在IOS中实现可折叠/展开的Table Section
author: Mars Shen
header-img: img/Header/coder-doom-dos-code-hd.jpg
catalog: true
multilingual: false
tags:
  - 原创
  - 翻译
  - 互联网技术
  - IOS开发
  - swift
abbrlink: 95d092ba
date: 2016-06-06 12:00:00
---

<blockquote>原文链接以及源代码:<a href="https://github.com/jeantimex/ios-swift-collapsible-table-section">原文地址</a><br/>原文作者:Yong Su @ Box Inc.</blockquote>
# 如何在IOS中实现可折叠/展开的Table Section?
<img src="https://img.shields.io/badge/Swift-2.2-orange.svg?style=flat" width="60">
<img src="{{ site.baseurl }}/img/IOSection/demo.gif" width="60%" heigh="60%">
<!-- more -->

#### 第一步:准备数据
<p>
假设我们有下面这些分好组的数据,每一组是一个 <code>Section</code> 对象
```Swift
struct Section {
  var name: String!
  var items: [String]!
  var collapsed: Bool!

  init(name: String, items: [String], collapsed: Bool = false) {
    self.name = name
    self.items = items
    self.collapsed = collapsed
  }
}

var sections = [Section]()

sections = [
  Section(name: "Mac", items: ["MacBook", "MacBook Air", "MacBook Pro", "iMac", "Mac Pro", "Mac mini", "Accessories", "OS X El Capitan"]),
  Section(name: "iPad", items: ["iPad Pro", "iPad Air 2", "iPad mini 4", "Accessories"]),
  Section(name: "iPhone", items: ["iPhone 6s", "iPhone 6", "iPhone SE", "Accessories"])
]
```
<code>collapsed</code> 表明了现在是否是折叠状态,默认为false.
</p>
#### 第二步:设计 Header 和 Cell
<p>在story board中选择 <code>Table View</code>, 选择 <code>Dynamic Prototypes</code>并且设置 <code>Prototype Cells</code> 的值为 2, 一个给我们的自定义 Header,另外一个给我们每一行的 Cell, 然后将各自的 <code>Identifier</code> 设置为为 header 和 cell.</p>
<img src="{{ site.baseurl }}/img/IOSection/cell.png" width="60%" heigh="60%">
<p>添加一个 UIButton (用做切换折叠状态) 和一个 Label 到 header 的 prototype cell 中, 创建一个继承自 <code>UITableViewCell</code> 的 swift 文件并且命名为<code> CollapsibleTableViewHeader.swift</code>. 这个文件很简单, 有两个 IBOutlets, 一个用做链接切换按钮 (UIButton), 另外一个用做链接 Label. 最后我们设置这个 header 的 Class 为 我们自定义的类 <code>CollapsibleTableViewHeader</code> 并且在类中正确的链接 IBOutlets.</p>
<p>现在这个文件应该看起来像这样:</p>
```Swift
import UIKit

class CollapsibleTableViewHeader: UITableViewCell {

  @IBOutlet var titleLabel: UILabel!
  @IBOutlet var toggleButton: UIButton!

}
```
<p>创建一个 prototype cell 和一个继承自 UITableViewCell 的类, 有这两点好处:</p>
<ul>
<li>我们能可视化的设计我们的 header</li>
<li>我们不需要像这样为了创建一个 Cell 而创建一个 nib 文件 然后在 tableView 中注册他使用:</li>
</ul>
```Swift
let nib = UINib(nibName: "TableSectionHeader", bundle: nil)
tableView.registerNib(nib, forHeaderFooterViewReuseIdentifier: "TableSectionHeader")
```
<p>个人而言, 我不喜欢在我的项目中有 nib 文件, 并且如果我们用了 <code>dequeueReusableHeaderFooterViewWithIdentifier</code>, 好像我们必须在那个 section 中最少拥有一行 row, 但这里我们需要 0 row!</p>
#### 第三步: UITableViewDelegate
<p>首先,用 sections.count 正确的表示 section 的数量:</p>
```Swift
override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
  return sections.count
}
```
<p>对于那些每个 section 中的 row 的数量, 我们使用 <code>collapsed</code> 变量去控制它, 如果 collapsed 是 true, 我们就返回 0, 否则返回他真实 items 的数量:</p>
```Swift
override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return (sections[section].collapsed!) ? 0 : sections[section].items.count
}
```
<p>我们使用 tableView 的 viewForHeaderInSection 方法去实现我们自定义的 header:</p>
```Swift
override func tableView(tableView: UITableView, viewForHeaderInSection section: Int) -> UIView? {
  let header = tableView.dequeueReusableCellWithIdentifier("header") as! CollapsibleTableViewHeader

  header.titleLabel.text = sections[section].name
  header.toggleButton.tag = section
  header.toggleButton.addTarget(self, action: #selector(CollapsibleTableViewController.toggleCollapse), forControlEvents: .TouchUpInside)

  header.toggleButton.rotate(sections[section].collapsed! ? 0.0 : CGFloat(M_PI_2))

  return header.contentView
}
```
<p>注意我们为我们的接切换按钮 (UIButton) 注册了一个touch up inside 事件, 一旦这个按钮被按下, 它将会触发 toggleCollapse 方法.</p>
<p>最后, 我们的 row cell 看起来像这样:</p>
```Swift
override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
  let cell = tableView.dequeueReusableCellWithIdentifier("cell") as UITableViewCell!

  cell.textLabel?.text = sections[indexPath.section].items[indexPath.row]

  return cell
}
```
<p>这个是 toggleCollapse 方法:</p>
```Swift
func toggleCollapse(sender: UIButton) {
  let section = sender.tag
  let collapsed = sections[section].collapsed

  // Toggle collapse
  sections[section].collapsed = !collapsed

  // Reload section
  tableView.reloadSections(NSIndexSet(index: section), withRowAnimation: .Automatic)
}
```
<p>好啦,就是这么简单,如果你需要看源代码,请到顶部原文链接中,你可以在那个链接中找到具体的实现代码</p>

<p><b>更多的可折叠Demo</b></p>
<p>有时你可能要在grouped-style table中实现一些可折叠的cells, 我有一些单独的Demo在<a href="https://github.com/jeantimex/ios-swift-collapsible-table-section-in-grouped-section">链接</a>中. 他的实现和今天我们学的这个非常相似,只有一点点小不同,如果有兴趣可以去看下.</p>

<img src="{{ site.baseurl }}/img/IOSection/otherDemo.gif" width="60%" heigh="60%">
