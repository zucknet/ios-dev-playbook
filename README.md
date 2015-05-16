# iOS Dev Playbook

[![Build Status](https://img.shields.io/travis/lexrus/ios-dev-playbook/master.svg?style=flat-square)](https://travis-ci.org/lexrus/ios-dev-playbook)
[![GitHub tag](https://img.shields.io/github/tag/lexrus/ios-dev-playbook.svg?style=flat-square)](https://github.com/lexrus/ios-dev-playbook)
![License](https://img.shields.io/github/license/lexrus/ios-dev-playbook.svg?style=flat-square)
![Love](https://img.shields.io/badge/build%20with-%3C3-ff69b4.svg?style=flat-square)

[![Git](https://img.shields.io/badge/GitHub-lexrus-blue.svg?style=flat-square)](https://github.com/lexrus)
[![Twitter](https://img.shields.io/badge/twitter-@lexrus-blue.svg?style=flat-square)](http://twitter.com/lexrus)
[![LinkedIn](https://img.shields.io/badge/linkedin-Lex Tang-blue.svg?style=flat-square)](https://cn.linkedin.com/in/lexrus/en)
[![eMail](https://img.shields.io/badge/email-lexrus@gmail.com-blue.svg?style=flat-square)](mailto:lexrus@gmail.com?SUBJECT=About iOS Dev Playbook)

作为一个 iOS 开发人员，会一些基础的运维技术能使工作环境更加顺手，并且为创业团队节省不少运维成本。这个 [Ansible](http://www.ansible.com) Playbook 的仓库，可以用来快速配置 iOS 开发需要的一些服务。当然，如果经费充裕，直接使用优质的收费服务更省时间。

因为精力有限，大部分 roles 只在 64bit __Debian Jessie__(Debian 8) 上跑通部署测试，理论上 Debian Wheezy 和 Ubuntu 14.04 以上也能用。请先阅读 [Ansible 的入门文档](http://docs.ansible.com)，不然遇到问题可能会没有方向。如果你用 Mac OS X，建议在 [Dash](http://kapeli.com/dash) 里安装 Ansible 的文档。

## 服务列表

#### [Docker](https://docker.com) `rake docker`
目前大部分服务都没有运行在 Docker 里。理想的运行方式是每一个服务都以 Docker container 的形式运行，互不干扰。等有空了再改。

#### [Gogs](http://gogs.io) :80 `rake gogs`
用 Go 语言编写的 Git 服务，特点是功能精简和速度快，树莓派也能流畅运行。我自己就在用，用的人比较少的话内存占用稳定在 100M 左右。`rake gogs` 安装后默认开了注册功能，如果要禁用，运行 `rake gogsdr` 即可。

#### [GitLab](https://github.com/gitlabhq/gitlabhq) :80 `rake gitlab`
知名的开源 Git 服务，特点是功能全面。安装完成后默认用户名是 `root`, 密码为 `5iveL!fe`。如果要同时安装 [GitLab-CI](https://about.gitlab.com/gitlab-ci/)，请把 `install_ci` 设置成 `True`。GitLab 对内存有一定要求，建议使用最少 1G 内存的主机，不过我在执行 GitLab 的 role 前加了设置 swap 等于两倍内存的 role，一般 512M 内存的 VPS 也能撑住 5 人以下的小团队，但是一般运维会建议不要使用 swap; 另外，旧的 omnibus 版本在安装时会被自动备份、升级，详见 `roles/gitlab/tasks/main.yml`，手动操作可以看[这里](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/update.md)。

#### [Countly Server](https://github.com/Countly/countly-server) :80 `rake countly`
知名的开源统计服务，如果你不想用第三方服务，它是不错的选择。

#### [Jenkins](http://jenkins-ci.org) :8080 `rake jenkins`
开源的持续集成服务，很多人用它来做自动编译打包 IPA 的事。不过界面不好看，我个人更喜欢 Atlassian Bamboo。

#### [Ghost](https://ghost.org/) :80 `rake ghost`
如果你想同时建一个 Blog 记录开发中的心得或者创业的艰辛，Ghost 是个不错的工具。它比 Wordpress 更轻巧，皮肤也都非常简洁。默认会自动装上这些皮肤: [ghostium](https://github.com/oswaldoacauan/ghostium)、[ghostrayder](https://github.com/k9ordon/ghostrayder)、[ghostwriter](https://github.com/roryg/ghostwriter)、[GhostScroll](https://github.com/grmmph/GhostScroll)、[Readium](https://github.com/starburst1977/Readium)

#### [NewRelic agent](https://newrelic.com) `rake newrelic`
懒人监控方案，先注册一个 NewRelic 帐号，所有服务器都运行这个 role，再装个 NewRelic iPhone 版，各种预警就有了。缺点是 agent 占用内存比较夸张，个人的这些小服务其实没有必要使用。到是可以装后面提到的 Uptime 或 Huginn 来实现简单的监控。

#### [Uptime](http://www.redotheweb.com/uptime/) :8082 `rake uptime`
默认用户 `root`，密码 `admin`。非常轻巧的服务器可用率监控服务，监控小团队的服务可用率肯定是够了。如果不想自己搭的话推荐用完全免费的 [UptimeRobot](https://uptimerobot.com) 代替。更全面的监控应该用 [Munin](http://munin-monitoring.org) / [Nagios](http://www.nagios.org) / [Sensu](http://sensuapp.org) 等工具实现。

#### [Ajenti](http://ajenti.org) :8000 `rake ajenti`
这是服务器管理工具，功能全，比较重。如果喜欢把所有服务都装在一起的话，装上这个也不错。默认用户 `root`，密码 `admin`。Ajenti 强烈建议使用 SSL 连接，但是 Safari 访问非 443 端口使用自签证书的服务器会比较麻烦，所以我暂时禁用了，可以进管理界面打开。不使用 SSL 有安全隐患，请避免在生产环境使用这样的配置。另外，重签证书可以用 `ajenti-ssl-gen hostname.com -f; service ajenti restart`;

#### [Huginn](https://github.com/cantino/huginn) :5000 `rake huginn`
算是 IFTTT 的 geek 版本，可以用来定制各种触发条件，可以自动完成很多事。默认用户 `admin`，密码 `password`。

#### [Shadowsocks](https://github.com/clowwindy/shadowsocks) `rake shadowsocks`
知名的代理服务，iOS 开发上网必备。还有 `rake cow` 可以安装同类竞品 [COW](https://github.com/cyfdecyf/cow)。另外一些日常上网用的 VPN 服务配置可以移步 [vpn-deploy-playbook](https://github.com/lexrus/vpn-deploy-playbook)。

#### [Seafile](http://seafile.com) :80 `rake seafile`
开源的文件共享服务，有各种平台的客户端，适合网络比较慢的公司在内网搭着用来共享文件，我个人还是倾向于用 Dropbox。默认帐号: `seafile@localhost` 密码: `seafile`。[目前有一个 bug](https://github.com/ginsys/ansible-role-seafile/issues/8) 会造成文件目录没有创建，需要手动创建。

#### [MediaWiki](http://www.mediawiki.org/) :80 `rake mediawiki`
最知名的 Wiki 程序。这个服务使用 docker 安装，安装后访问安装界面配置好 LocalSettings.php 后下载，把下载的文件放在 `roles/mediawiki/files` 目录下，再运行 `rake mediawiki_settings` 就可以把新的配置文件传到服务器上。

#### [Gem in a Box](https://github.com/geminabox/geminabox) :9922 `rake geminabox`
在内网建一个 RubyGems 镜像，加速 Rails 开发。这个 role 没有做全面的测试，可能无法正常使用。使用内网或本地 RubyGems 镜像时，一般不想修改 Gemfile，那么可以这样操作(注意替换地址): `bundle config mirror.https://rubygems.org http://localhost:9922 ; bundle config mirror.http://rubygems.org http://localhost:9922`

## 准备加入的服务

- [Phabricator](http://phabricator.org) Facebook 的项目管理工具。
- [RedMine](http://www.redmine.org) 团队 WiKi 服务。
- [Let's Chat](http://sdelements.github.io/lets-chat/) 或者 [friends](https://github.com/moose-team/friends) 团队在线聊天工具，可是为什么不用 Slack 呢？
- [Cachet](https://cachethq.io) 服务状态页面，如果有不少 Web 服务的话，会需要这样的服务。
- [Vault](https://github.com/hashicorp/vault) 密钥管理工具。
- [Sparkler](https://github.com/mackuba/sparkler) 如果同时开发 OS X 程序并使用 Sparkle 来更新版本，这个服务可以用来收集升级数据。
- [apt-cacher-ng](https://docs.docker.com/examples/apt-cacher-ng/) 使用 docker 必备的服务。

## 使用方法

1. `sudo pip install ansible docker-py` - 安装 Ansible (版本至少 1.8 以上)
1. `git clone https://github.com/lexrus/ios-dev-playbook.git` - 下载项目
1. `cd ios-dev-playbook` - 进入目录
1. `cp ansible_hosts.ini{.example,}` - 复制 ansible_hosts.ini.example 到 ansible_hosts.ini，然后修改相应的服务器地址
1. 如果要备份这些配置，可以用 `rake dropbox`，它会把重要的配置备份到 `~/Dropbox/.ios-dev-playbook` 目录下
1. 确保你的服务器可以用 [SSH key 验证登录](http://www.debian-administration.org/article/530/SSH_with_authentication_key_instead_of_password)
1. 安装相应的服务，如 GitLab: `rake gitlab`，更多命令用 `rake -T` 列出
1. 如果遇到问题可以试着用 `rake deps` 更新第三方 roles，用 [Ansible Galaxy](https://galaxy.ansible.com) 更新依赖的 roles([.ansible_galaxy_dependencies](https://github.com/lexrus/ios-dev-playbook/blob/master/.ansible_galaxy_dependencies))
1. `rake jessie` 自动升级 Debian Wheezy 没有经过测试，不保证能成功

## 注意事项

1. 备份策略是: 等有空了再备份 @__@#
2. 各个服务的使用方法这里就不赘述了，我在上面的列表里加了相应的链接;
5. 端口都是 :80 的服务不能装在一起，再设置一个二级域名吧;

## 测试

测试需要 [Vagrant](https://www.vagrantup.com/)，在本项目目录中 `rake test_ubuntu` 就会拉一个 ubuntu/trusty64 的镜像试着跑大部分配置，测 Debian Wheezy 请用 `rake test_debian`。
如果你用的是 Mac OS X，推荐先装 [Brew](http://brew.sh) 和 [Cask](http://caskroom.io)，然后运行 `brew cask install vagrant virtualbox`。
Vagrant 测试的 roles 都写在 [tests/vagrant_test.yml](https://github.com/lexrus/ios-dev-playbook/blob/master/tests/vagrant_test.yml) 里了。

本地用 Vagrant 测试会消耗很多时间，尤其是在网速不理想的情况下。并且，请确保你的电脑有至少 2G 的空闲内存。

Travis 设置了测试 playbook 的语法是否正确，以及 `001_common_utilities.yml` 是否能顺利跑通。


## 赞助

如果这个项目为你省下很多时间，那就请我喝一杯咖啡吧。我的 PayPal 和支付宝帐号都是: `lexrus@gmail.com`


## 上哪搞服务器?

欢迎你使用我的推广链接注册自己的虚拟服务器：

[我的 DigitalOcean 推广链接](https://www.digitalocean.com/?refcode=3eb5cf371fc9) 新加坡节点延时都很低，每月 5 刀起，通过推广链接注册就送 10 刀。

[我的 Vultr 推广链接](http://www.vultr.com/?ref=6822054) 最低每月 5 刀，使用优惠码 SSDVPS 就送 20 刀。

[我的 Linode 推广链接](http://www.linode.com/?r=9f144941e797d495a10c2841c3137ce1acde5f15) Linode 虽然贵一点(每月 10 刀起)，但是服务非常稳定，性能也不错。


## 协议

本项目的源码遵循 MIT 协议，详见 `LICENSE` 文件。
