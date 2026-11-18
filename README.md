# 微信码工具

一个纯前端的微信二维码「换装」工具：上传微信收款码（或任意二维码），换中间的头像、加名称和文案、挑页面配色，导出高清 PNG。**二维码内容一个字节都不改，扫出来还是原来那个码。**

- 无需服务器、无需数据库，一个 `index.html` 就能跑
- 所有处理都在浏览器本地完成，图片不会上传
- 打开即用，改完即时预览，并自动做一次「扫码自检」

## 功能

**收款码换装**
- 上传收款码 / 赞赏码 / 加好友码截图，自动识别内容
- 两种模式：
  - **高清重绘**（推荐）：用识别出的内容重建一张干净的二维码（纠错级别 H），顺便把模糊截图洗清晰
  - **保留原图**：直接用原图局部裁剪，像素忠于原码
- 换中间头像：任意图片，圆形 / 圆角方，8%–30% 尺寸可调，自动加白边
- **名称、副标题、上方小字都可以不填**，留空照样能生成
- 5 套页面配色：素白 / 苔绿 / 米砂 / 雾蓝 / 墨黑
- 导出 2160px 宽的 PNG；微信内打开时提供「长按保存」

**个人名片二维码**
- 姓名 / 职位 / 公司 / 手机 / 邮箱 / 网址 / 地址 / 一句话介绍
- 生成标准 vCard 3.0 二维码（UTF-8），扫码即可存进通讯录
- 同样支持头像、配色与导出

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
git commit -m "feat: 微信码工具"
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

**微信里点了下载没反应？**
微信内置浏览器不支持直接下载，点「长按保存」，长按图片存到相册即可。

**名片码扫出来是什么？**
一个 vCard 联系人卡片，iOS / Android 扫码后可以直接「存储联系人」。微信扫一扫会以文本形式展示，可复制后用通讯录打开。

---

© gdhkj · 部署于 GitHub Pages
