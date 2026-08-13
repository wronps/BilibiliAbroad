# 海外流畅看 B 站

在海外看 b 站卡的原因是，b 站的海外 cdn服务器不多，而运营商的 dns （我也测了 google 和 Cloudflare 的，一样不行）还经常分配到垃圾 cdn 上，我这里测到甚至直接分配到国内的 cdn 上了。。。

所以，与其单纯折腾 DNS，更直接的办法是：**把 B 站视频请求重定向到自己测试下来速度比较好的 CDN。**

> CDN 的实际表现和地区、运营商、时段都有关系，所以这里记录的节点不一定适合所有人。

---

## 网页端

网页端我使用 **Bilibili-CDN 加速**扩展。

它可以测试当前视频可用的 CDN，并帮助选择速度更好的服务器。

这一部分基本属于傻瓜式操作，就不展开写了。

重点是记下测试出来表现比较好的 CDN 域名，例如：

```text
upos-sz-mirror08c(.bilivideo.com), 括号里的内容被省略了.
```

后面手机端会用到。

---

## iPhone / iPad

我这里只有苹果设备，所以这里记录的是 iOS / iPadOS 的方案。

需要使用一个支持：

* 模块
* URL Rewrite / Script
* HTTPS 解密（MitM）

的网络工具。

以Shadowrocket为例：

进入：

```text
配置 → 模块 → 右上角 +
```

添加 BiliUniverse Redirect 模块。

 输入

```text
https://github.com/BiliUniverse/Redirect/releases/download/v0.2.20/BiliBili.Redirect.sgmodule
```

添加以后，找到：

```text
📺 BiliBili: 🔀 Redirect
```

点击最左边的参数信息按钮，然后选择 **更新参数**。

---

## 参数配置

假设前面测试出来比较快的 CDN 是：

```text
upos-sz-mirror08c.bilivideo.com
```

我这里的配置是：

```text
Host.OverseaVideo = upos-sz-mirror08c.bilivideo.com
Host.BStar        = upos-sz-mirror08c.bilivideo.com
Host.PCDN         = upos-sz-mirror08c.bilivideo.com

Host.MCDN         = proxy-tf-all-ws.bilivideo.com

Storage           = Argument
LogLevel          = WARN
```

其中：

* `Host.OverseaVideo`：港澳台相关视频 CDN
* `Host.BStar`：Bilibili 国际版相关 CDN
* `Host.PCDN`：PCDN 请求的重定向目标
* `Host.MCDN`：MCDN 请求的重定向目标
* `Storage = Argument`：优先使用模块参数中的配置
* `LogLevel = WARN`：只保留警告及更高等级的日志

我这里 `MCDN` 保持默认的：

```text
proxy-tf-all-ws.bilivideo.com
```

---

## 开启 HTTPS 解密

返回配置主页。

在本地配置文件（默认一般是 `default.conf`）右侧进入详情，找到 **HTTPS 解密**相关设置并启用，然后按照系统提示安装并信任证书。

完成以后，模块才能对对应的 B 站视频请求进行 Rewrite / Script 处理。

配置好之后, 不需要开 vpn, 正常用就行.

---

## 最后

这样一来，我现在网页端和 iPhone / iPad 上看 B 站都是流畅 4k, 甚至拖动进度条都不卡加载的。

不过需要注意：


不同节点的负载、运营商路由以及跨境网络情况都会变化。今天最快的服务器，过一段时间不一定还是最快的。一个视频最快的服务器，别的视频也不一定还是最快的。我这里测下来百度和华为的都不错。

如果之后又开始卡，可以重新在网页端测试 CDN，然后把：

```text
Host.OverseaVideo
Host.BStar
Host.PCDN
```

换成新的节点。

无论如何，年费大会员泪目～

---

> 本文主要是我自己的配置记录和经验分享，并不是完整教学。
>
> 不同地区、运营商和软件版本的情况可能不同，具体配置建议自行测试。
>
> 有不明白的地方可以善用 AI 工具辅助理解。
