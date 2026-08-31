# 壁纸集 · Wallpaper Gallery

一个轻量、响应式的**壁纸展示网站**单页应用（SPA）。基于 **Vite + React 18 + Tailwind CSS** 构建，
纯静态产物，可直接部署到 **GitHub Pages**。

- 瀑布流网格展示所有壁纸缩略图（懒加载）
- 按**分类**横向筛选（"全部" + 各分类 chips）
- 关键词**实时搜索**（匹配分类名 / 图片名）
- 点击缩略图打开**全屏灯箱**：上一张 / 下一张、键盘 ← → 导航、Esc 关闭、下载原图
- 移动端单列/双列、桌面多列，大字号、留白舒适的 UI

## 目录结构

```
.
├── index.html
├── package.json
├── vite.config.js          # base: './'（兼容 GitHub Pages 子路径）
├── postcss.config.js
├── tailwind.config.js
├── .github/workflows/
│   └── deploy.yml          # GitHub Pages 自动部署
├── public/
│   ├── manifest.json       # 由 scripts/compress.py 生成（数据管线）
│   └── wallpapers/<cid>/{thumb,full}/<i>.webp
├── scripts/
│   └── compress.py         # 批量压缩 + 生成 manifest.json
└── src/
    ├── main.jsx
    ├── App.jsx
    ├── index.css
    ├── hooks/useGalleryData.js
    └── components/
        ├── Header.jsx
        ├── SearchBar.jsx
        ├── CategoryNav.jsx
        ├── Gallery.jsx
        ├── Lightbox.jsx
        └── States.jsx
```

## 本地运行

要求 Node.js 18+（部署工作流使用 Node 20，本地也可使用受管 Node）。

```bash
# 安装依赖（生成项目级 node_modules）
npm install

# 启动开发服务器（默认 http://localhost:5173）
npm run dev
```

> 开发时需先准备壁纸数据：运行 `scripts/compress.py` 生成 `public/manifest.json`
> 与 `public/wallpapers/**`（详见下方「重新生成壁纸数据」）。
> 若数据尚未生成，页面会显示「加载失败 / 清单为空」提示，属正常情况。

## 构建

```bash
npm run build      # 产物输出到 dist/
npm run preview    # 本地预览构建产物
```

`vite.config.js` 中已设置 `base: './'`，因此 `dist/` 内的资源与图片引用均为相对路径，
无论仓库以根路径还是 `<repo>` 子路径托管均可正确加载。构建仅复制 `public/` 内容，
**即使 `public/wallpapers` 下的 webp 尚未生成完毕，构建依然会通过**。

## 部署到 GitHub Pages

本项目已内置 `.github/workflows/deploy.yml`，使用官方 Pages 部署动作：

1. 在仓库 **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**。
2. 将代码推送到 `main` 分支（或到 Actions 页面手动 **Run workflow**）。
3. 工作流会执行 `npm ci` → `npm run build` → 上传并部署 `dist/` 到 GitHub Pages。

部署完成后访问 `https://<用户名>.github.io/<仓库名>/` 即可。

## 重新生成壁纸数据

壁纸图片与 `manifest.json` 由数据管线脚本生成，不在前端仓库内手写为代码：

```bash
# 依赖 Pillow
pip install Pillow

# 修改脚本顶部的 SRC（源图目录）后运行
python scripts/compress.py
```

脚本会遍历源目录下的每个分类文件夹，将每张原图转为两档 WebP：

- `thumb`：最长边 500px、质量 70（网格缩略图）
- `full`：最长边 2000px、质量 82（灯箱大图）

并输出 `public/manifest.json`，结构示例：

```json
{
  "generatedAt": "2025-01-01T00:00:00",
  "source": "D:\\壁纸",
  "total": 965,
  "categoryCount": 257,
  "categories": [
    {
      "id": "bx_001",
      "name": "时光悠悠",
      "cover": "wallpapers/bx_001/thumb/1.webp",
      "count": 4,
      "images": [
        { "thumb": "wallpapers/bx_001/thumb/1.webp", "full": "wallpapers/bx_001/full/1.webp", "name": "原文件名.png" }
      ]
    }
  ]
}
```

`manifest.json` 与 `wallpapers/` 属于生成物，请随仓库一并提交；如体积过大，
也可仅提交 `manifest.json` 并将 `wallpapers/` 改为通过 Release / 对象存储分发（前端读取路径不变）。

## 技术栈

- 构建：[Vite](https://vitejs.dev/) 5
- 框架：[React](https://react.dev/) 18
- 样式：[Tailwind CSS](https://tailwindcss.com/) 3
- 部署：GitHub Pages（Actions 自动部署）

## 许可证

仅供学习与个人展示使用。壁纸版权归原作者所有。
