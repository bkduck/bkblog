# 腾讯云轻量服务器k3s搭建hexo博客

最近想入手学习`k8s`，经网上一顿猛如虎搜索后，奈何钱包不够，只好选择腾讯云轻量服务器 2g4核的`k3s`集成环境。`CI/CD`持续集成选择了`GitHub Actions` ，部署好后，只需提交分支到`GitHub`就自动构建镜像并且发布到线上服务器。至于镜像仓库，还是秉持白嫖原则，使用腾讯云自带的镜像仓。最后博客的框架是`Hexo + Fluid`静态博客，评论系统则使用`Waline`。

线上博客请移步[www.bkduck.cn](https://bkduck.cn)

看官们若想折腾一翻，请移步下方教程，倒腾一翻，反正首年40元即可获得,马上戳下面链接领取呗~

[【腾讯云】爆款2核2G云服务器首年40元，企业首购最高获赠300元京东卡](https://cloud.tencent.com/act/cps/redirect?redirect=1577&cps_key=3f5175eeea52ff27432360605d2c7f81&from=console)

![](https://static.bkduck.cn/img/blog/202203051540191646466019_Rk0cKE.jpg)


## 事前准备
* 本地部署好博客环境`hexo` + `fluid`,具体方法自行google或后期再出一期教程
* 了解`GitHub Actions` 的基本语法，可以移步看看阮一峰老师的文章[《GitHub Actions 入门教程》](https://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
* 缺少点Like的`Github`账号，收藏三连防丢失 [github](https://github.com/bkduck/bkblog)
* 腾讯云一键初始化的轻量服务器k3s环境，请按照官方文档一顿操作 [文档](使用应用镜像实践 K3s 容器集群管理)
* 免费的线上腾讯云镜像仓库，照旧按官方文档走一遍 [《管理自定义镜像》](https://cloud.tencent.com/document/product/1207/53038)

## 实际操作

### k3s环境部署

登录线上k3s的环境，执行 Ingress，Deployment，Service创建

```
>> rz -be  # 选择deployment.yaml,上传到服务器
>> kubectl apply -f deployment.yaml	# 创建必须的资源
```

### 代码修改

镜像仓库调整，全局替换`ccr.ccs.tencentyun.com/xxx/blog` 变成你的镜像仓地址

### GitHub Action环境参数配置

创建`action secrets`, 然后在`.github/workflows/*.yaml` 替换成你创建的secrets

![](https://static.bkduck.cn/img/blog/202203051632411646469161_CMhSta.png)

一切就绪，push分支到`Github`,剩下构建镜像，推送镜像，重启k3s的deployment的工作就交给`Github Actions`完成，cafe啜一口，你的博客构建完成呗！

### waline 评论系统的搭建

方案很多，可以百度搜下，我的方案是k3s部署`mysql`，当然生产环境不推荐，原因你懂的，但毕竟看荷包的活，毕竟不用钱，你也可以额试试部署`mysql-*.yaml` + `deployment.yaml`



