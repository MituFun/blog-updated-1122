# Fuwari 博客升级记录

迁移日期：2026-11-22。

## 基线

- 原博客：`MituFun/fuwari`，`dfc1413`。
- 上游：`https://github.com/saicaca/fuwari`，主分支 `6d39b0dec41282e7852e23e032998a5789abee28`。
- 共同祖先：`cb6f97f`，本次纳入之后的 72 个上游提交。
- 使用 Git 三方合并，保留双方历史，方便以后继续合并更新。
- 原目录 `../fuwari` 未修改。

## 保留的定制

- 全部原有文章、文章图片、About 内容和 404 文案。
- 站点地址 `https://blog.mitufun.top/`、中文、标题、个人资料和导航链接。
- 头像、favicon、横幅图片及作者署名，固定主题色与默认深色模式。
- CC BY-NC-SA 4.0 配置、Umami 统计脚本与网站 ID。
- Codeforces 图标所需的 `@iconify-json/simple-icons` 依赖。
- 原有辅助文件也随历史保留；`.autoupdate.bat` 中的强制推送命令未执行。

## 冲突处理

- 使用上游新版依赖与锁文件，补回个人图标依赖。
- 使用个人站点配置，并补入新版 `expressiveCodeConfig`。
- 删除上游示例文章，避免出现在个人博客。
- 404 页改为仅由 `MainGridLayout` 提供外层布局，避免重复 HTML、导航及统计脚本。
- 统计脚本显式使用 `is:inline`，保留外部脚本属性。

## 本地使用

```sh
pnpm install --frozen-lockfile
pnpm dev
pnpm check
pnpm build
pnpm preview
```

`pnpm build` 包含 Pagefind 搜索索引生成，发布产物为 `dist/`。

## 以后同步上游

`upstream` 指向 Fuwari 官方仓库；`origin` 指向原博客仓库。新目录尚未推送或部署。

在提交本地改动之后执行：

```sh
git fetch upstream
git merge upstream/main
pnpm install
pnpm check
pnpm build
```

出现冲突时保留个人配置，同时补充上游新增的配置字段。

## 验证

- `pnpm check`：56 个文件，0 错误、0 警告、0 提示。
- `pnpm build`：静态页面及 Pagefind 中文搜索索引生成成功。
- 逐文件 SHA-256 核对原文章、文章图片、头像和 public 资源，内容一致。
- 修复上游主题切换组件的空 props 类型、归档分类的 nullable 类型及筛选参数默认值，并清理代码块插件的无效参数。
- 原文章的 KaTeX 中文数学字符及旧 Browserslist 数据可能产生非阻塞提示；正文保持原样。

未向远程推送，未修改线上部署。
