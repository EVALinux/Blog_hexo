---
title: 基于hexo搭建博客
---

搭建博客的步骤参考[GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249).
原教程完成于2019年，部分步骤已经有了一些改变。
所以这里列出来基于上述教程的一些注意事项。

后续有了新的更新，也会在这篇文档中进行增补和修正。

2021-02-02  首次提交:搭建的相关说明。

2021-02-03  更新:增加从远程库clone后无法正常使用hexo进行调试的说明。

<!-- more -->
---
* 绑定域名

  绑定域名环节中，原文会使用` 192.30.252.153`作为域名解析的记录值。但现在使用这个地址会收到GitHub的警告邮件，原文如下:

  ```
  The page build completed successfully, but returned the following warning for the `master` branch:

  The custom domain for your GitHub Pages site is pointed at an outdated IP address. You must update your site's DNS records if you'd like it to be available via your custom domain. For more information, see https://  docs.github.com/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site.

  For information on troubleshooting Jekyll see:

  https://docs.github.com/articles/troubleshooting-jekyll-builds

  If you have any questions you can submit a request on the Contact GitHub page at https://support.github.com/contact?repo_id=335141802&page_build_id=230873589
  ```

  每次部署都会收到这样的邮件，按照邮件提示，我们使用的IP地址已过时。最终按照提示找到了新的地址：

  ```
  185.199.108.153
  185.199.109.153
  185.199.110.153
  185.199.111.153
  ```

* 绑定域名之后每次部署都会覆盖掉我们的CNAME文件的问题

  绑定域名之后，在XXX.github.io仓库中我们可以看到自动创建的CNAME文件，里面记录了我们绑定的域名。但这个文件仅仅存在我们的远程仓库中，而Hexo的部署环节是自动的，导致每次提交的时候都会重新生成public目录来覆盖掉我们当前的远程仓库。最终导致我们每次提交都会导致我们绑定域名的步骤失效。

  解决方案:

  将CNAME文件复制到source目录下，这样自动生成的public目录也会带有CNAME文件。提交时就不会出现远程仓库的CNAME文件消失的问题了。

* 保存项目与保存博客

  Hexo自动提交的是生成的博客本身，是我们项目的产物。而项目本身则不会自动提交到远程保存，所以我们还需要在github上再建立一个新的仓库来保存项目。下面是博客项目的托管地址。其中themes/next是当前我们使用的主题。为了防止嵌套别人的git仓库在我们的代码里，所以在.gitignore中剔除了这部分，需要下载好我们项目代码之后再自己下载这部分代码。
  ``` bash
  #此流程存在问题，请看下一个
  git clone https://github.com/EVALinux/Blog_hexo.git

  cd Blog_hexo

  git clone https://github.com/theme-next/hexo-theme-next themes/next
  ```

---

2021-02-03:

* 工程从远程仓库下载到本地后，因为提交不包含`node_modules`目录，所以无法直接在当前目录中使用hexo相关指令。

  解决方案:

  成功完成node.js和hexo的安装后。首先使用hexo在本地初始化一个空的博客项目。将空项目中的node_modules拷贝到Blog_hexo目录下即可。
  ``` bash
  git clone https://github.com/EVALinux/Blog_hexo.git

  hexo init test

  #cp ./test/node_modules ./Blog_hexo/  #这里用的Linux指令，Windows下直接复制粘贴即可

  #rm -rf ./test/

  cd Blog_hexo

  git clone https://github.com/theme-next/hexo-theme-next themes/next

  hexo g

  hexo s
  ```

* hexo 及npm部分操作耗时过长的问题
  npm安装插件默认是从国外服务器下载，网络不稳定，速度慢，耗时长。

  解决方案:
  切换国内源。
  ``` bash
  npm config set registry https://registry.npm.taobao.org #切换源

  npm config get registry #查询是否切换成功，成功的话应该是显示上一条的链接
  ```
