# 基于 Hexo 的个人博客

使用 [Hexo](https://hexo.io/) 搭建的静态博客，支持 Markdown 写作、主题与插件扩展。

## 环境要求

- Node.js 14+
- npm 或 yarn

## 安装与运行

```bash
# 安装依赖（若尚未安装）
npm install

# 本地预览（默认 http://localhost:4000）
npm run server

# 生成静态文件到 public/
npm run build

# 清理缓存后重新生成
npm run clean && npm run build
```

## 常用命令

| 命令 | 说明 |
|------|------|
| `hexo new "文章标题"` | 在 `source/_posts/` 下创建新文章 |
| `hexo new page "关于"` | 创建新页面（如关于页） |
| `hexo server` | 启动本地服务器 |
| `hexo generate` | 生成静态站点 |
| `hexo deploy` | 部署（需先配置 _config.yml 的 deploy） |

## 目录结构

```
├── _config.yml       # 站点配置（标题、语言、主题等）
├── source/
│   ├── _posts/       # 博客文章（Markdown）
│   └── ...           # 其他页面与资源
├── themes/           # 主题目录（当前使用 landscape）
└── public/           # 生成后的静态文件（可部署）
```

## 配置说明

- **站点信息**：在 `_config.yml` 中修改 `title`、`author`、`language`（如 `zh-CN`）、`timezone`（如 `Asia/Shanghai`）。
- **主题**：默认使用 `landscape`，可改为 [Hexo 主题](https://hexo.io/themes/) 或自定义主题。
- **部署**：在 `_config.yml` 的 `deploy` 中配置 Git、Vercel、Netlify 等，然后执行 `hexo deploy`。

## 写文章

1. 执行 `npx hexo new "我的第一篇文章"` 或手动在 `source/_posts/` 下新建 `.md` 文件。
2. 在文件顶部写 YAML 头信息，例如：

```yaml
---
title: 文章标题
date: 2025-03-02 12:00:00
tags:
  - 标签1
  - 标签2
categories:
  - 分类名
---
```

3. 正文使用 Markdown 书写，保存后刷新本地预览即可看到效果。

## 从 WordPress 迁移数据

本项目已安装 [hexo-migrator-wordpress](https://github.com/hexojs/hexo-migrator-wordpress)，可按以下步骤把 WordPress 内容迁到 Hexo。

---

### 情况一：只有数据库、打不开 WordPress 后台

如果你只有 WordPress 的**数据库**（MySQL 备份或能连上数据库），无法登录后台做「工具 → 导出」，可以用本项目的**从数据库导出 WXR** 脚本。

#### 1. 确保能访问 MySQL 数据库

- 若有 **.sql 备份文件**：先把它导入到本机或服务器上的 MySQL，得到可连接的数据库（主机、端口、库名、用户名、密码）。
- 若本来就是**远程/本机 MySQL**：记下主机、端口、库名、用户名、密码。

#### 2. 填写数据库配置

在项目根目录复制示例配置并改名为实际配置（**不要提交到 Git**，已加入 .gitignore）：

```bash
copy wordpress-db.config.example.json wordpress-db.config.json
```

用编辑器打开 `wordpress-db.config.json`，按你的环境填写：

| 字段 | 说明 |
|------|------|
| `host` | 数据库主机，如 `localhost` 或 `127.0.0.1` |
| `port` | 端口，默认 `3306` |
| `user` | 数据库用户名 |
| `password` | 数据库密码 |
| `database` | WordPress 使用的数据库名 |
| `tablePrefix` | 表前缀，默认 `wp_`（若安装时改过请填写实际前缀） |
| `siteUrl` | 原博客地址，如 `https://你的博客.com` |
| `siteTitle` / `siteDescription` | 可选，导出 XML 里的站点信息 |

#### 3. 从数据库导出 WXR 再迁移到 Hexo

在项目根目录执行：

```bash
# 从数据库生成 wordpress-export.xml
npm run export:wordpress-db

# 再用 Hexo 迁移到 source/_posts/
npx hexo migrate wordpress wordpress-export.xml
```

也可指定配置和输出路径：

```bash
node scripts/wordpress-db-to-wxr.js --config=wordpress-db.config.json --output=my-export.xml
npx hexo migrate wordpress my-export.xml
```

脚本会导出所有 **已发布** 的 `post` 和 `page`，以及分类、标签，生成标准 WXR 格式，供 `hexo migrate wordpress` 使用。

---

### 情况二：能登录 WordPress 后台

#### 1. 在 WordPress 后台导出

1. 登录 WordPress 后台
2. 进入 **工具 → 导出**
3. 选择 **“所有内容”**（或只导出文章）
4. 点击 **“下载导出文件”**，得到 `.xml` 文件（如 `wordpress-2025-03-02.xml`）

#### 2. 在本项目中执行迁移

把导出的 XML 文件放到项目目录（或记下路径），然后执行：

```bash
# 方式一：使用 npm 脚本（-- 后面接你的 XML 文件路径）
npm run migrate:wordpress -- 你的导出文件.xml

# 方式二：直接使用 hexo 命令
npx hexo migrate wordpress 你的导出文件.xml
```

例如文件在项目根目录且名为 `wordpress-export.xml`：

```bash
npm run migrate:wordpress -- wordpress-export.xml
```

### 迁移可选参数（情况一、二导出 XML 后都可用）

| 参数 | 说明 |
|------|------|
| `--alias` | 为每篇文章生成别名，便于做 301 重定向（保留旧链接） |
| `--limit=N` | 只迁移前 N 篇文章（用于试跑） |
| `--skipduplicate` | 跳过标题重复的文章 |
| `--import-image` | 下载并替换文章中的 WordPress 图片链接到本地 |

示例（下载图片并限制 10 篇试跑）：

```bash
npx hexo migrate wordpress wordpress-export.xml --import-image --limit=10
```

### 4. 迁移完成后

- 文章会出现在 `source/_posts/` 下，为 Markdown 格式，含 front matter（标题、日期、分类、标签等）
- 若有分类/标签，可在 `_config.yml` 或主题中按需调整
- 执行 `npm run server` 本地预览，确认无误后再 `npm run build` 和部署

### 5. 迁移后图片不显示

文章里的图片若仍指向原站（如 `http://原博客/wp-content/uploads/...`）或第三方图床，可能无法访问。可用本项目的**图片本地化脚本**把远程图下载到 `source/images/` 并替换链接：

```bash
# 先预览会处理哪些图片（不下载、不修改文章）
npm run images:download -- --dry-run

# 执行下载并替换文章中的图片链接
npm run images:download
```

脚本会扫描 `source/_posts/` 下所有文章中的 `![](url)` 和 `<img src="url">`，将能访问到的图片保存到 `source/images/`，并把文中链接改为 `/images/xxx`。无法访问的 URL 会保留原样并输出“失败”日志。

## 部署

### 当前方案：GitHub + 主站 gxlself.com/blog/

博客已配置为在 **https://gxlself.com/blog/** 下访问（`_config.yml` 中 `url: https://gxlself.com`、`root: /blog/`）。

1. **在 GitHub 建仓库**  
   仓库名建议为 `blog`，例如：`https://github.com/gxlself/blog`。  
   （若仓库名不同，请改 `_config.yml` 里 `deploy.repo`。）

2. **生成并推到 GitHub**  
   ```bash
   npm run build
   npx hexo deploy
   ```
   会把 `public/` 推送到该仓库的 **gh-pages** 分支。GitHub Pages 若开启，可先通过 `https://gxlself.github.io/blog/` 访问验证。

3. **在主站 gxlself.com 挂载 /blog/**  
   任选其一即可：

   - **反向代理（推荐）**  
     在主站（Nginx / Caddy / 云函数等）里把 `https://gxlself.com/blog/` 反向代理到 `https://gxlself.github.io/blog/`。  
     Nginx 示例：
     ```nginx
     location /blog/ {
       proxy_pass https://gxlself.github.io/blog/;
       proxy_set_header Host gxlself.github.io;
       proxy_ssl_server_name on;
     }
     ```

   - **直接拷贝构建结果**  
     本地执行 `npm run build` 后，把生成的 `public/` 目录里的全部文件上传到主站服务器的 `/blog/` 目录（或你站点根目录下的 `blog` 文件夹），由主站直接提供静态文件。

完成后访问 **https://gxlself.com/blog/** 即可打开博客。

---

### 其他部署方式（仅博客独立访问时）

- **GitHub Pages 独立站**：仓库名改为 `username.github.io`，`url` 改为 `https://用户名.github.io/`，`root: /`，部署到 `main` 分支的 `public/` 或使用 `gh-pages` 分支。
- **Vercel / Netlify**：连接本仓库，构建命令 `npm run build`，发布目录 `public`，并在 `_config.yml` 中把 `url` 设为该平台提供的域名（若要做在 gxlself.com/blog/ 下，需在主站做反向代理到 Vercel/Netlify 的地址）。
