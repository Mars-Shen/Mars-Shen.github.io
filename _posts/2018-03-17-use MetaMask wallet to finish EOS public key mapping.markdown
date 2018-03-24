---
layout:     post
title:      "使用MetaMask钱包完成EOS钱包映射"
subtitle:   ""
date:       2018-03-16 12:00:00
author:     "Mars Shen"
header-img: "img/Header/eos-hd.jpg"
catelog: true
tags: [EOS]
---

<blockquote>前提条件:
	<ul>
		<li>科学上网</li>
		<li>Chrome浏览器</li>
	</ul>
</blockquote>

<h2 class="section-heading">是否需要钱包映射?</h2>
<p>
	关于是否需要映射的问题, 网上已经有很多讨论了, 个人在这里给出的建议是, 如果你嫌麻烦可以直接把EOS放在交易所, 据我所知现在主流的交易所都会帮你完成映射过程, 但如果你想把交易所的EOS提出来保存到自己的钱包, 那还是需要做钱包映射的, 因为EOS项目目前还没有完成, 目前市场上的EOS为项目开发组预发行的EOS代币, 并不是真正的EOS币, 在2018年6月2日6:59:59（北京时间）此前钱包里流通的EOS代币将会被冻结, 真正的EOS币将会投放到对应钱包取代这些代币, 如果钱包不做映射, 你将不会得到这些真正的EOS货币.
</p>
<h2 class="section-heading">使用MetaMask钱包完成EOS钱包映射</h2>
<blockquote>映射分三步:
	<ul>
		<li>钱包的生成</li>
		<li>EOS公钥私钥的生成</li>
		<li>签署智能合约, 完成映射</li>
	</ul>
</blockquote>
<p>
	网上关于使用MyEtherWallet完成钱包映射的文章有很多, 但是使用MetaMask钱包完成EOS钱包映射的文章却不多, 本文主要介绍使用MetaMask钱包完成EOS钱包映射. MetaMask钱包是Chrome的一个插件, 非常方便, 同时也是EOS官网推荐的钱包之一. 映射过程比MyEtherWallet方便很多.
</p>
<p><b>·安装MetaMask钱包插件, 生成钱包</b></p>
<p>
	这一步很简单, 首先在Chrome插件中心搜索MetaMask并且安装. 
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/metamask.png" width="80%" heigh="80%">
<p>
	打开这个插件. 点击接受协议, 第二次协议出现时, 需要将协议滚到最下面接受按钮才会亮:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/metamaskstep1.png" width="60%" heigh="60%">
<p>
	创建一个新密码, 这个密码是用来登陆本地钱包账户的, 请牢记:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/metamaskstep2.png" width="60%" heigh="60%">
<p>
	他会给你12个英文单词, 找个安全的地方记好这12个单词, 因为这是唯一恢复你钱包的方法, 完了之后点击<code>I'VE COPIED IT SOMEWHERE SAFE</code>进入下一步:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/metamaskstep3.png" width="60%" heigh="60%">
<p>
	至此钱包部分就已经完成了, 你可以自己探索下这个钱包, 找到钱包的私钥保存好, 不要泄露.
</p>
<p><b>·EOS公钥私钥的生成</b></p>
<p>
	这一步网上有很多工具可以一键生成EOS公钥和私钥, 但我没用过, 所以这里说的是官网生成方法, 首先进入EOS官网, 点击GOT. 注意这个按钮必须科学上网才能点, 因为EOS规定美国公民和中国公民无法参加这个EOS项目, 所以你得找个不是美国以及中国的ip才能点这个按钮.
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/geteos.png" width="90%" heigh="90%">
<p>
	勾选所有选项, 进入下一页, 找到<code>Participation Instructions</code>选择<code>REGISTER</code>:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/regbutton.png" width="90%" heigh="90%">
<p>
	选择<code>METAMASK</code>, 打开你的Chrome MetaMask插件, 然后在网页上点击<code>EOS token distribution</code>按钮:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/regmaskinfo.png" width="90%" heigh="90%">
<p>
	这里页面上左边一栏从上到下分别是合约地址,你钱包的地址,以后映射完你EOS公钥的地址,EOS余额. 然后点击<code>REGISTER EOS KEY</code>按钮, 系统会生成一对新的EOS公钥和私钥, 请牢记你的公钥和私钥, 特别是私钥, 不要给别人, 自己妥善保管:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/RegisterEOSkey.png" width="90%" heigh="90%">
<p>
	妥善处置好你的私钥后, 将私钥填入确认框, 点击下一步:
</p>
<p><b>·签署智能合约, 完成映射</b></p>
<p>
	当点击下一步以后, 系统会自动生成一个智能合约, 签署智能合约的过程就类似于一次交易, 需要一定的GAS, 也就是一定数量的ETH才能完成, 如果你没有ETH, 请先冲ETH进入你的钱包:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/signcontract.png" width="70%" heigh="70%">
<p>
	点击提交, 完成智能合约签署, 签署过程的时间由你给的GAS决定, 给的GAS越大, 处理起来越快, 我这里给了1GWEI, 处理了大概一晚上.
</p>
<p>
	如果想看进度, 可以点击MetaMask右上角三个点, 查看账户信息:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/viewaccount.png" width="70%" heigh="70%">
<p>
	这个页面可以看到详细的账户信息, 可以看到交易现在的状态:
</p>
<img src="{{ site.baseurl }}/img/EOSMapping/accountinfo.png" width="100%" heigh="100%">
<p>
	智能合约签署完成, 一般也就意味着映射的完成, 如何查看自己映射成功呢? 在刚才注册的网页, 也就是<code>EOS token distribution</code>页面上, 如果之前为空的EOS public key一项变成了你刚才生成的EOS公钥, 那就说明钱包与EOS公网已经完成映射.
</p>



