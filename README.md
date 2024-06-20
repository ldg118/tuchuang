# telegraph-Image


### [Demo](https://img.131213.xyz/)

### 开发计划
- [x] 后台管理
- [x] 鉴黄
- [ ] 画廊
- [ ] referer、IP黑名单


### 优点

1. 无限图片储存数量，你可以上传不限数量的图片

2. 无需购买服务器，托管于Cloudflare的网络上，当使用量不超过Cloudflare的免费额度时，完全免费

3. 无需购买域名，可以使用Cloudflare Pages提供的*.pages.dev的免费二级域名，同时也支持绑定自定义域名

4. 支持图片审查API，可根据需要开启，开启后不良图片将自动屏蔽，不再加载

5. 支持后台图片管理，日志管理，查看访问前20的Referer、IP、img,可以对上传的图片进行在线预览，添加白名单，黑名单等操作


### 更新日志

+ 2023-8-21 : 支持自建鉴黄api

+ 2023-8-13 ：图片访问日志,数据库表已修改 更新时请先删除表`imginfo`重新创建

+ 2023-8-12 ：图片管理功能

### 利用Cloudflare pages部署

> [开启图片管理功能教程](./docs/manage.md)

1. 点击[Use this template](https://github.com/x-dr/telegraph-Image/generate)按钮创建一个新的代码库。

2. 登录到[Cloudflare](https://dash.cloudflare.com/)控制台.
3. 在帐户主页中，选择`pages`> ` Create a project` > `Connect to Git`
4. 选择你创建的项目存储库，在`Set up builds and deployments`部分中，全部默认即可。

<img src="https://img.131213.xyz/file/beb0385822e24c9a9d459.png"  height="50%" width="50%" />

5. 点击`Save and Deploy`部署，然后点`Continue to project`即可看到访问域名

>  访问http(s)://你的域名/admin 即可打开后台管理页面

>  访问http(s)://你的域名/list 即可打开log管理页面


#### 后台管理不是很完善 但基本的应该都有了 

**[开启图片管理功能教程](./docs/manage.md)**


---
---
### 利用vercel部署(vercel分支)

[![Deploy with Vercel](https://vercel.com/button?utm_source=busiyi&utm_campaign=oss)](https://vercel.com/new/clone?utm_source=busiyi&utm_campaign=oss&repository-url=https://github.com/x-dr/telegraph-Image/tree/vercel)

---





### 自定义cdn加速
> 默认是使用cloudflare ,修改 `asset/js/upload.js#L219` 即可

+ 如用cachefly加速 

cachefly绑定cloudflare pages
<img src="https://img.131213.xyz/file/c19f7ea17ce2027b13dfa.png" />

修改代码

```diff
- const PROXYURL = ""  //自定义加速域名 默认是使用cloudflare
+ const PROXYURL = "https://xxxxxxxxxx.cachefly.net"  //自定义加速域名 默认是使用cloudflare
```

通过 Telegraph 与赛博菩萨提供的 Pages、D1，搭建一个专属于你自己的开源图床，如果你有更多需求还可通过优选加速图片载入时间，更有接入ModerateContent提供的审查图像内容的API key，过滤涩涩内容！

优点
无限图片储存数量，你可以上传不限数量的图片 but！单张图片不能超过5MB
无需服务器，托管于Cloudflare的网络上，当使用量不超过Cloudflare的免费额度时，完全免费
无需域名，可以使用Cloudflare Pages提供的*.pages.dev的免费二级域名，同时也支持绑定自定义域名
支持图片审查API，可根据需要开启，开启后不良图片将自动屏蔽，不再加载
支持后台管理，日志管理，查看访问前20的Referer、IP、img,可以对上传的图片进行在线预览，添加白名单，黑名单等操作
开始部署
1. Pages 部署 telegraph-Image 项目
打开telegraph-Image仓库项目，先给作者点击Star后再点击Fork！可以增加成功率！！！手动狗头
回到 Workers 和 Pages > 概述 > 创建 > Pages > 连接到Git > 选择telegraph-Image项目 > 保存并部署即可
2. 绑定自定义域
这里推荐优先使用已经转入CF的域名，并开启小黄云。如果你没有域名，也可以退而求其次使用CNAME方式使用免费域名接入自定义域。
回到 Workers 和 Pages /telegraph-Image项目 > 设置 > 函数 > 放置 > 制作 > 智能 > 保存
3. 创建管理后台
3.1 回到 Workers 和 Pages > D1 > 创建数据库 > 仪表盘 > 数据库名称img(名称可取任意值) > 创建
3.2 进入img数据库 > 控制台 > 粘贴以下代码后 > 点击执行 > 等待提示此查询已成功执行。
DROP TABLE IF EXISTS tgimglog;
CREATE TABLE IF NOT EXISTS tgimglog (
    `id` integer PRIMARY KEY NOT NULL,
    `url` text,
    `referer` text,
    `ip` varchar(255),
    `time` DATE
);
DROP TABLE IF EXISTS imginfo;
CREATE TABLE IF NOT EXISTS imginfo (
    `id` integer PRIMARY KEY NOT NULL,
    `url` text,
    `referer` text,
    `ip` varchar(255),
    `rating` text,
    `total` integer,
    `time` DATE
);
3.3 回到 Workers 和 Pages /telegraph-Image项目 > 设置 > 函数 > D1 数据库绑定 > 变量名IMG > img数据库 > 点击保存
3.4 回到 Workers 和 Pages /telegraph-Image项目 > 设置 > 环境变量 > 为生产环境定义变量 > 变量内容如下:
变量名BASIC_USER，值为你的后台管理员用户名
变量名BASIC_PASS，值为你的后台管理员密码
无需审查涩涩内容可跳过这一步
打开ModerateContent API申请页面，输入你的邮箱后点击SUBMIT
前往你的邮箱将 ModerateContent.com Sup 邮件内的 API Key 复制出来
变量名ModerateContentApiKey，值为你的 API Key
点击保存
3.5 回到 Workers 和 Pages /telegraph-Image项目 > 部署 > 右下角三个点 > 重试部署即可
如何使用
例如：你的 Pages自定义域 为img.131213.xyz

https://img.131213.xyz 为 图床上传使用地址
https://img.131213.xyz/admin 为 图床后台管理地址
https://img.131213.xyz/list 为 图床访问日志
变量说明
变量名	示例	备注
BASIC_USER	admin	后台管理员用户名
BASIC_PASS	123456	后台管理员密码
ModerateContentApiKey	8ba353957d6c2bea538dca28a66a04cd	审查图像内容的API key
RATINGAPI	https://xxx.xxx/rating	自建的鉴黄api
注意优先级 RATINGAPI > ModerateContentApiKey




### 感谢

[@cf-pages](https://github.com/cf-pages/Telegraph-Image)

[@likebeta](https://github.com/likebeta/telegraph-image-hosting)




