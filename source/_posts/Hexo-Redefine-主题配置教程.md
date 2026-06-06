---
title: Hexo + Redefine 主题配置教程
date: 2026-06-07 01:30:00
tags:
  - Hexo
  - Redefine
  - 教程
  - 博客
categories:
  - 技术
cover: /images/posts/redefine-config/banner.webp
description: 从 Stellar 主题迁移到 Redefine v2.9.0 的完整实战记录。包含主题切换、个性化配置、踩坑经验。
---

> 本文假设你已经有一个能跑起来的 Hexo 博客(用默认的 landscape 主题或别的什么主题都行)。如果你还没有博客,先去 [Hexo 官网](https://hexo.io) 走一遍"30 秒搭建"流程再回来看。

## 这是什么

Redefine 是 GitHub 用户 [EvanNotFound](https://github.com/EvanNotFound/hexo-theme-redefine) 维护的一款 Hexo 主题,设计感强、动画细腻、配置项多,适合做**个人向**的博客(技术笔记、生活记录都行)。本文记录我从 Stellar 主题迁到 Redefine 的全过程 + 踩过的坑,小白跟着做能复现我的博客样式。

最终效果:[blog.billnas.cn](https://blog.billnas.cn/)

---

## 目录

1. [为什么换主题](#1-为什么换主题)
2. [安装 Redefine](#2-安装-redefine)
3. [切换主题](#3-切换主题)
4. [理解配置文件结构](#4-理解配置文件结构)
5. [基础信息 info](#5-基础信息-info)
6. [头像 avatar](#6-头像-avatar)
7. [主页横幅 home_banner](#7-主页横幅-home_banner)
8. [导航栏 navbar](#8-导航栏-navbar)
9. [页脚 footer](#9-页脚-footer)
10. [文章封面 cover](#10-文章封面-cover)
11. [访问计数 website_counter(踩坑警告)](#11-访问计数-website_counter踩坑警告)
12. [评论系统 comment](#12-评论系统-comment)
13. [常见问题](#13-常见问题)

---

## 1. 为什么换主题

我之前用的是 [Stellar](https://github.com/xaoxuu/hexo-theme-stellar),简洁但功能少。后来看到 [krazyu.com](https://krazyu.com) 用了 Redefine 主题,觉得:
- **首页横幅动画**(打字机效果)很精致
- **侧边栏头像 + 简介**布局舒服
- **深色/浅色模式**自动切换
- **SPA 单页切换**(SWUP),点链接不刷新

就动了换主题的念头。

---

## 2. 安装 Redefine

在博客根目录执行:

```bash
cd 你的博客目录
npm install hexo-theme-redefine --save
```

> Redefine v2.9.0 的安装方式**就是** npm,不用 `git clone`。这是它和很多 Hexo 主题不一样的地方。

安装完,看 `node_modules/hexo-theme-redefine/` 下有没有内容,有就说明装好了。

---

## 3. 切换主题

打开博客根目录的 `_config.yml`,找到 `theme` 字段,改成:

```yaml
theme: redefine
```

**重要**:Redefine 的配置**不**写在 `_config.yml` 里,而是写在博客根目录的 `_config.redefine.yml`。需要从主题目录复制默认配置:

```bash
# Windows (PowerShell)
copy node_modules\hexo-theme-redefine\_config.yml _config.redefine.yml

# macOS / Linux
cp node_modules/hexo-theme-redefine/_config.yml _config.redefine.yml
```

这个 `_config.redefine.yml` 就是**你改主题配置**的地方,大概有 400+ 行,所有可改项都在里面。

---

## 4. 理解配置文件结构

打开 `_config.redefine.yml`,你会看到很多 `# 注释`,这些是说明,告诉你每个字段什么意思。整个文件按功能分块,常见的有:

| 块名 | 作用 |
|---|---|
| `info` | 站点的标题、副标题、作者、语言、URL |
| `global` | 全局开关(单页模式、懒加载等) |
| `defaults` | 默认值(头像、封面、logo) |
| `home_banner` | 首页横幅(标题、副标题、打字机文字、背景图) |
| `navbar` | 顶部导航栏 |
| `articles` | 文章页相关(图懒加载、代码高亮等) |
| `website_counter` | 访问计数(Busuanzi/VerCount) |
| `comment` | 评论系统(支持 waline/twikoo/giscus 等) |
| `footer` | 页脚(运行时间、备案号) |
| `inject` | 注入自定义 HTML/JS |

**改之前**:用 `Ctrl+F` 搜字段名(比如 `avatar`),看周围注释和默认值,不要瞎改。

---

## 5. 基础信息 info

打开 `_config.redefine.yml`,找 `info:` 块,改这几个:

```yaml
info:
  title: 小华的博客
  subtitle: 记录生活与思考
  author: 小华
  language: zh-CN
  url: https://blog.billnas.cn
  description: 一个普通人的博客
  keywords: 博客,Hexo,技术
```

- `title`:浏览器标签 + 首页大标题
- `subtitle`:首页副标题(小字)
- `author`:文章作者署名
- `url`:站点 URL(必填,影响 RSS / sitemap / OG 图)
- `language`:中文填 `zh-CN`

---

## 6. 头像 avatar

把头像图片放到 `source/images/` 下(可以是 jpg/png,推荐转 webp 减小体积),然后:

```yaml
defaults:
  avatar: /images/你的头像.webp
```

> 路径**必须以 `/` 开头**,这是 Hexo 把 `source/images/` 映射到网站根目录 `/images/` 的规则。`/images/x.webp` 对应磁盘上 `source/images/x.webp`。

**怎么把 jpg 转 webp?**

```python
from PIL import Image
Image.open(r'C:\path\原图.jpg').save(
    r'E:\blog\source\images\新图.webp', 'WEBP', quality=85)
```

quality 85 是肉眼无损 + 体积小的平衡点。

---

## 7. 主页横幅 home_banner

这块决定首页最上面那块大 banner 长什么样。关键字段:

```yaml
home_banner:
  enable: true
  title: 「终不似,少年游」
  title_size: 3.5rem       # 标题字号
  subtitle:
    enable: true
    text:                  # 打字机效果会循环这些
      - 昨夜西风凋碧树
      - 独上高楼
      - 望尽天涯路
      - ...
  image:
    light: /images/banner-light.webp   # 浅色模式背景
    dark: /images/banner-dark.webp     # 深色模式背景
```

**坑**:
- `title` 里的逗号如果你想用**全角「,」**(中文标准),在 YAML 里要确保编码正确,不然会被吞成半角。最稳妥是直接复制粘贴中文逗号
- `text` 列表项**不要**带引号(YAML 列表格式),例:`- 昨夜西风凋碧树`,不是 `- "昨夜西风凋碧树"`
- `title_size` 单位是 `rem`,3.5rem ≈ 56px,够大

---

## 8. 导航栏 navbar

```yaml
navbar:
  links:
    Home: /
    Archives: /archives
    Tags: /tags
    Categories: /categories
    About: /about
```

注意:Tags、Categories、About 这三个**页面要你自己建**,不然点了会 404。

新建 `source/tags/index.md`:

```yaml
---
title: 标签
date: 2026-06-03 16:54:11
type: tags
---
```

`source/categories/index.md` 和 `source/about/index.md` 同理,把 `type:` 分别改成 `categories` 和 `about`。

---

## 9. 页脚 footer

```yaml
footer:
  start: 2026/6/3 16:54:11   # 站点"运行时间"从这算起
  ICP:                          # 工信部备案号(国内服务器才有)
    enable: false
```

**别**乱改 `start`,这是页脚"博客已运行 X 天"的起点。

---

## 10. 文章封面 cover

在文章的 markdown 文件**最上面**的 front-matter 里加:

```yaml
---
title: 我的文章
date: 2026-06-07 12:00:00
cover: /images/posts/my-post/banner.webp
---
```

封面图按**每个文章建子目录**管理,放 `source/images/posts/文章名/banner.webp`,避免命名冲突。

**支持的字段**(任选其一):
- `cover`:封面图
- `banner`:同 cover
- `thumbnail`:`true` 自动用文章第一张图,字符串 = 指定图,`false` = 不显示

---

## 11. 访问计数 website_counter(踩坑警告)

Redefine 主题用 [VerCount](https://vercount.one/)(替代了停服的 Busuanzi)做访问统计。配置:

```yaml
website_counter:
  url: https://cn.vercount.one/js
  enable: true
  site_pv: true      # 站点总访问量
  site_uv: true      # 站点独立访客
  post_pv: true      # 文章阅读量
```

**开启后页脚会显示**:
```
访问人数 41065444 | 总访问量 64368671
```

---

### 🚨 我踩过的坑

我一开始想给显示的数字加个偏移(从 1000/2000 开始),在 `inject.footer` 里写了一段 JS:

```javascript
// ❌ 错误写法:会用 setInterval 反复加偏移
setInterval(function() {
  var uv = document.getElementById('busuanzi_value_site_uv');
  if (uv) uv.innerText = (parseInt(uv.innerText) + 1000).toLocaleString();
}, 2000);
```

**结果**:每 2 秒数字 +1000,几分钟后变成 99999999。

**根因**:`setInterval` 每次都读**已经被加过偏移的** innerText,再加一遍,无限累加。

**正确写法**:**只读一次真实值,记到 `dataset` 里,之后别再碰 DOM**。但更简单的是:**直接把显示文字改成"1000+""2000+"**(用 `MutationObserver` 永久覆盖)。

或者**最干脆**:**关掉这个功能**(`enable: false`),页脚干干净净。

---

## 12. 评论系统 comment

Redefine 支持好几种评论系统,配置里切换:

```yaml
comment:
  enable: false        # 默认关
  system: waline       # 候选: waline / twikoo / giscus / gitalk / utterances
```

| 方案 | 国内访问 | 访客要账号吗 | 免费 |
|---|---|---|---|
| **Waline** | ★★★☆☆ | 否 | 是(需部署后端) |
| **Twikoo** | ★★★☆☆ | 否 | 是(需部署后端) |
| **Giscus** | ★★★★☆ | **要 GitHub** | 是(无需后端) |
| **Utterances** | ★★★★☆ | **要 GitHub** | 是(无需后端) |

**部署评论 = 你搭个后端**(因为静态博客存不了评论数据)。最省事的是 Vercel / Netlify / Hugging Face 部署 Twikoo,5 分钟搞定。

如果你只是个人博客、不想折腾,先关着(`enable: false`)也行,以后再加。

---

## 13. 常见问题

### Q: 改了 `_config.redefine.yml` 但本地没生效?

**A**:Hexo 不会热加载主题配置。改完执行:

```bash
npx hexo clean && npx hexo generate
```

然后**重启** hexo server(`Ctrl+C` 停掉再启)。

### Q: 启动 hexo server 报"端口 4000 被占用"?

**A**:杀掉占用进程:

```bash
# Windows
netstat -ano | grep ":4000"        # 找 PID
powershell -Command "Stop-Process -Id <PID> -Force"

# macOS / Linux
lsof -i:4000
kill -9 <PID>
```

### Q: 文章没显示封面图?

**A**:检查三处:
1. 图片在 `source/images/...` 下存在
2. front-matter 路径以 `/` 开头(不是 `./`)
3. 重新 `hexo clean && hexo generate`(老 `public/` 缓存)

### Q: YAML 报错?

**A**:YAML 对缩进极其敏感,**必须**用 2 个空格,不能用 Tab。报错信息一般会指到行号,比如:

```
YAMLException: end of the stream or a document separator is expected at line 120
```

打开 `_config.redefine.yml` 看 120 行附近。

### Q: 部署到 GitHub 后没生效?

**A**:看 GitHub Actions 标签页的执行日志(`.github/workflows/` 下的 yml 文件),看哪一步报错了。常见坑:
- 腾讯云 COS 密钥配错
- `git push` 没触发(看 Actions 是不是 enabled)

---

## 总结

Redefine 主题的**优势**:
- ✅ 配置项丰富,但默认值合理,小白也能用
- ✅ 视觉效果好(动画、过渡、懒加载一应俱全)
- ✅ 文档完善([Redefine 文档](https://redefine-docs.ohevan.com/))
- ✅ 社区活跃(GitHub Issues 响应快)

**适合谁**:
- 想做**个人博客**(技术 / 生活 / 摄影都行)
- 愿意花 2-3 小时折腾配置
- 不追求极简(Redefine 不算极简风)

**不适合谁**:
- 追求极致性能(Redefine 有不少动画,会拖慢 LCP)
- 只想写不想配(用默认 landscape 主题就行)

---

## 参考资料

- [Redefine 官方文档](https://redefine-docs.ohevan.com/)
- [Redefine GitHub 仓库](https://github.com/EvanNotFound/hexo-theme-redefine)
- [VerCount 官方](https://vercount.one/)
- [Hexo 官方文档](https://hexo.io/docs/)

有其他问题欢迎在评论区问我(如果开了评论的话)或者邮件。
