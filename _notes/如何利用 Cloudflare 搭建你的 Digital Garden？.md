---
title: 如何利用 Cloudflare 搭建你的 Digital Garden？
date: 2025-06-16
---
# 1. 什么是 Cloudflare 与 Digital Garden？
[Cloudflare](https://zh.wikipedia.org/zh-cn/Cloudflare)是位于旧金山的全球网络服务商。常因为其部分服务免费且量大管饱被称为”互联网活菩萨“。如果你想搭建一个能够全球访问的静态网站，并且不想为此付费或者花费太多时间，那 Cloudflare Pages 真是太适合你了。往常如果你想搭建这样的博客，你需要在国内云服务商那里购买一台云服务器和那小小的带宽，或者使用 Github Pages 忍受在国内飘忽不定的访问稳定性，在你想要和朋友炫耀时总是收到无法加载的反馈。而在 Cloudflare 不光通通免费，更是得益于其全球节点与CDN缓存，加载速度与体验更是上乘。

Digital Garden 归根到底就是一个 Blog，往往会被特化为管理”个人或共享知识“的 Blog，但是我想就把它当作你的微信公众号吧，虽然无人查阅却让人产生”共享“错觉的公众号😂。
# 2. Clone 到你的 Github Repository
Digital Garden 存在许多模版，今天我们使用[Maximevaillancourt 的 digital-garden-jekyll-template](https://github.com/maximevaillancourt/digital-garden-jekyll-template)直接打开这个链接，并且点击页面上方的 Fork 按钮。这可以在你的 Github 仓库中创建一个一模一样的新仓库。
![[截屏2025-06-25 16.58.49.png]]

# 3. 部署到你的 Cloudflare Pages
对了，以防你不知道这是在干什么，这个网站的搭建流程是这样的：
1. 本地创建新的文章，或者修改网页。
2. 预览检查没问题就将其 Git 到你刚刚创建的仓库。
3. Cloudflare 惊奇地发现你修改了你的仓库。
4. Cloudflare 从你更改后的仓库拉取过来，你的仓库提供了原材料，但还不是可以直接查看的网页，所以 Cloudflare 会进行加工把原材料制作为网页，也就是部署。
5. 当当当，现在你就可以在全世界查看你的网页了。
所以现在你需要注册一个 Cloudflare 账户，这需要你有支付手段，但是幸好使用银联卡即可，如果还是不行就注册一个 PayPal 并使用银联卡作为支付手段。现在你就得到了一大堆免费服务。

进入 Cloudflare Pages 中，绑定你的 Github 仓库，后面你就不用管了，按照流程点击，Cloudflare 会帮你处理一切。
# 本地管理你的网站
你需要 Git Clone 你的仓库，把你的仓库拉取到本地。随后进入你的 Digital Garden 目录`cd my-digital-garden`。先通过`bundle`命令安装依赖项。随后可以使用此命令进行预览 `bundle exec jekyll serve`，打开[http://localhost:4000](http://localhost:4000/)即可查看你的网站。如果你认为就位，可使用下面的命令进行部署。
```
$ git add --all
$ git commit -m 'Update content'
$ git push origin main
```