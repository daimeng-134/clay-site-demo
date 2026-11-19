# clay-site-demo

定制开发公司官网的**可复刻演示站**，基于 [create-clay-site](https://www.npmjs.com/package/create-clay-site) 脚手架生成（Next.js 14 静态导出 + Warm Clay 0.2.1 复用包）。

这个仓库有两个用途：

1. **看效果** —— `docs/` 是构建产物，通过 GitHub Pages 直接访问
2. **给 AI 复刻** —— `kit/` 是打包好的素材，喂给豆包/Claude 之类就能生成一份近似的前端

---

## 在线预览

```
官网      https://daimeng-134.github.io/clay-site-demo/
交付页    https://daimeng-134.github.io/clay-site-demo/start.html
二维码    https://daimeng-134.github.io/clay-site-demo/qr.png
素材包    https://daimeng-134.github.io/clay-site-demo/kit/
```

给客户扫的二维码指向 `start.html`：里面有原站入口、素材包下载、一键复制指令、四步操作。

14 个静态页面：首页、服务、案例列表、4 个案例详情、关于、联系、404、robots、sitemap。

---

## 素材包（`kit/`）

| 文件 | 用途 |
| --- | --- |
| `snapshot.html` | 单文件快照：CSS、字体、图片全部内联，无脚本。模型能一次读完结构和样式 |
| `spec.md` | 区块顺序 + 逐字文案 + 断点，当施工图用 |
| `tokens.json` | 41 个设计 token（颜色 / 字号 / 行高 / 圆角 / 间距 / 阴影） |
| `prompt.txt` | 给模型的结构化指令，复制粘贴即可 |
| `fullpage-desktop.png` | 1440px 整页截图 |
| `fullpage-mobile.png` | 390px 整页截图 |
| `README.txt` | 给客户看的四步操作说明 |

### 怎么用

1. 打开豆包，新建对话
2. 上传 `snapshot.html`、`spec.md`、`fullpage-desktop.png`
3. 打开 `prompt.txt` 复制全文，粘贴发送
4. 豆包输出完整的 `index.html`，保存后双击即可预览

> 直接丢一个网址给模型，还原度大概三四成；上传这套素材能到九成。
> 原因是模型抓不到渲染后的 CSS、图片和字体，而这些在 `snapshot.html` 里是内联好的。

---

## 怎么重新生成

```bash
cd clay-site-starter
npm install                # 只装 CLI 自己的依赖（零依赖，其实可跳过）
cd template && npm install

# 1) 构建
npm run build                                    # 根路径版本 → out/
NEXT_BASE_PATH=/clay-site-demo npm run build     # 子路径版本（GitHub Pages 用）

# 2) 生成素材包
cd ..
node tools/make-clone-kit.mjs --dir template/out --shots shots
```

---

## 技术说明

- **静态导出**：`output: "export"`，产物是纯 HTML/CSS/JS，无 Node 运行时、无数据库
- **子路径支持**：`NEXT_BASE_PATH` 环境变量，不设就挂在根目录
- **内容与渲染分离**：所有文案在 `src/content/*.ts`，页面只负责渲染
- **样式覆盖层**：`src/styles/brand.css` 靠 import 顺序覆盖复用包 token，升级不会冲掉定制

### 已知问题

- 样式从 `fonts.googleapis.com` 拉取 Outfit / Noto Sans SC，国内网络下会失败并回退到系统字体。素材包里已把这条远程导入剥离，改用系统字体栈。
- 表单是纯前端组件，没有后端接收。

---

## 授权

模板 UI 源码的对外授权由项目所有者确认，本仓库不代为授予。
`src/warm-clay-icons/` 内含 Lucide 上游许可（`LICENSE.lucide-react.txt`），分发时保留。
