# 好彩U全工具箱

一个纯前端的二维码工具箱：上传任意收款二维码（或成品码图），换中间头像、加名称文案、换整卡配色与码色，导出高清 PNG；另附个人名片二维码生成。**二维码内容一个字节都不改，扫出来还是原来那个码。**

界面走 Apple 玻璃质感：工具入口是独立卡片，点击有按压反馈与轻柔音效（右上角可关）。

打开先看到一屏品牌开屏（深绿渐变 U 标识 + 一句隐私承诺），约 1 秒后自动进入，轻触可立即跳过；品牌标 `logo.svg` 可直接拿去用作仓库头像或 README 图。

- 无需服务器、无需数据库，一个 `index.html` 就能跑
- 所有处理都在浏览器本地完成，图片不会上传（顶栏下方有常驻隐私条：本机处理 · 不上传 · 关闭即清空）
- 打开即用，改完即时预览，并自动做一次「扫码自检」

## 功能

**收款码海报**
- 上传收款码 / 赞赏码 / 加好友码截图，自动识别内容
- 两种模式：
  - **高清重绘**（推荐）：用识别出的内容重建一张干净的二维码（纠错级别 H），顺便把模糊截图洗清晰
  - **保留原图**：直接用原图局部裁剪，像素忠于原码
- 换中间头像：任意图片，圆形 / 圆角方，8%–30% 尺寸可调，自动加白边
- **名称、标题、副标题都可以不填**，留空照样能生成
- 四种卡片模板：**品牌海报**（深绿渐变 + 白色卡片 + 商家标签 + 底部标识）、**极简卡**、**边框卡**（细边框 + 四角标记）、**票据卡**（锯齿票据造型）
- **底部标识可自定义**：默认「好彩U」，改名字会自动生成一枚与支付质感相符的 ¥ 徽标（颜色跟随卡片主题）；也可以上传自己的店铺 logo，或一键隐藏整块标识；留空则自动隐藏
- 二维码在白卡里始终居中、四周留白一致，1:1 / 3:4 / 9:16 三种比例都按可用空间自适应，不会出现偏左或大片空白
- 三种尺寸：1:1 方图 / 3:4 海报 / 9:16 长图
- 页脚是品牌信息 + 三枚隐私徽章（本机处理 / 不上传 / 关闭即清空），页面不放置任何外链
- 5 套卡片配色：素白 / 苔绿 / 米砂 / 雾蓝 / 墨黑，配色会渲染整张卡片（底色、标题、标签、底部版权区）
- 5 种二维码颜色：墨黑 / 深绿 / 深蓝 / 咖啡 / 酒红，只换码点颜色，白底与静区不变；码色按钮本身就是对应颜色的小二维码
- 点击音效与按压反馈（AudioContext 合成，无外部音频文件；右上角可一键静音）
- 手机端底部操作栏可一键收起成小悬浮按钮，不挡预览
- 导出 2160px 宽的 PNG（长图最高 3840px）；手机上提供「长按保存」

**个人名片二维码**
- 姓名 / 职位 / 公司 / 手机 / 邮箱 / 网址 / 地址 / 一句话介绍
- 生成标准 vCard 3.0 二维码（UTF-8），扫码即可存进通讯录
- 三种模板：**名片卡**（头像 + 昵称 + 地区 + 底部扫码提示）、**极简卡**、**简约卡**（头像居中、姓名职位上下排）
- 同样支持三种尺寸、5 套配色、5 种码色与导出

## 扫码自检

每次改动后，页面会把生成的图案重新解码一遍：

- `扫码通过 · 内容一致`：解出来的内容和原码完全相同
- `扫不出来 · 点我缩小头像`：头像压到了关键区域，点一下自动缩小

原理：二维码自带纠错能力，中间被遮住的面积只要在容错范围内，解码结果完全不变。头像默认 16%，上限 30%（在 H 纠错下依然安全）。

## 本地使用

下载仓库，双击 `index.html` 即可（不需要起服务）。

## 部署到 GitHub Pages

### 方式一：网页上传（不用装 git）

1. 打开 <https://github.com/new>，仓库名填 `wechat-qr-tool`，选 **Public**，创建
2. 进入仓库 → **Add file → Upload files**，把 `index.html`、`README.md` 拖进去提交
3. 再 **Add file → Create new file**，文件名填 `.nojekyll`，内容留空，提交（作用是关掉 Jekyll 处理，让静态文件原样发布）
4. 仓库 **Settings → Pages**：
   - Source 选 **Deploy from a branch**
   - Branch 选 **main**，目录选 **/ (root)**，Save
5. 等 1–2 分钟，访问 `https://gdhkj.github.io/wechat-qr-tool/`

### 方式二：命令行

```bash
git init
git add index.html README.md .nojekyll
git commit -m "feat: 好彩U 二维码工具箱"
git branch -M main
git remote add origin git@github.com:gdhkj/wechat-qr-tool.git
git push -u origin main
```

推完同样去 **Settings → Pages** 选 `main` + `/ (root)`。

### 方式三：GitHub CLI

```bash
gh repo create wechat-qr-tool --public --source=. --push
gh api --method POST repos/gdhkj/wechat-qr-tool/pages \
  -f "source[branch]=main" -f "source[path]=/"
```

### 换成自己的域名

仓库根目录加一个 `CNAME` 文件，写你的域名（如 `qr.example.com`），再到 DNS 加一条 CNAME 指向 `gdhkj.github.io`，最后在 Pages 里勾选 Enforce HTTPS。

## 二次开发

```
index.html            ← 成品，单文件、自包含（内含 jsQR + qrcode-generator）
_src/style.css        ← 样式
_src/body.html        ← 结构
_src/app.js           ← 全部逻辑（识别、绘制、自检、导出）
_src/shell.html       ← 打包模板
build.py              ← 打包脚本：python3 build.py
libs/                 ← 依赖源码（仅供打包，部署不需要）
```

改完源码执行 `python3 build.py` 重新生成 `index.html`。也可以直接改 `index.html`——JS 和 CSS 都在文件末尾、可读。

想调样式：CSS 变量都在 `:root` 里（主色 `--brand`、圆角、阴影）。
想调头像安全上限：`_src/body.html` 里 `#logoSize` 的 `max`，以及 `app.js` 里 `Math.max(8, ...)` 的兜底值。

## 隐私

不上传、不请求任何外部接口；没有埋点；断网也能用。上传的图片通过 `URL.createObjectURL` 只存在于当前页面内存，刷新即释放。

## 依赖

- [jsQR](https://github.com/cozmo/jsQR) — Apache-2.0，识别二维码
- [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) — MIT，生成二维码

两者都已内联进 `index.html`，不依赖任何 CDN。

## 常见问题

**加了头像为什么还能扫？**
二维码有纠错能力，遮住中心一小块不影响解码。工具会在每次改动后自动解码校验，通不过会提示你缩小头像。

**扫不出来怎么办？**
点一下「扫码通过」旁的红标，工具会自动缩小头像；或把「高清重绘」打开，重建一张更清晰的码。

**手机上点了下载没反应？**
部分内置浏览器不支持直接下载，点「长按保存」，长按图片存到相册即可。

**名片码扫出来是什么？**
一个 vCard 联系人卡片，iOS / Android 扫码后可以直接「存储联系人」。部分扫码应用会以文本形式展示，复制后用通讯录打开即可。

---

© gdhkj · 部署于 GitHub Pages
