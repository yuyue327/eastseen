# 看得见的东方 EastSeen — 官网

一个**纯静态单文件网站**：所有页面、动效、AI 生成引擎、二维码编码器全部写在 `index.html` 里，不依赖任何服务器、数据库和第三方接口。部署到 GitHub Pages 之后，任何人扫码即可访问，你的电脑不需要一直开机联网。

## 文件说明

```
看得见的东方/
├── index.html   ← 整个网站（唯一必须部署的文件）
├── logo.svg     ← 品牌标志矢量源文件（做头像、印刷用）
└── README.md    ← 本说明
```

## 一、本地预览（PyCharm / 终端）

```bash
cd /Users/mac/Desktop/看得见的东方
python3 -m http.server 8000
```

浏览器打开 `http://localhost:8000` 即可。停止服务按 `Ctrl + C`。
（PyCharm 里也可以直接双击 index.html 用浏览器打开，但强烈建议用上面这条命令，二维码和收藏功能表现和线上一致。）

## 二、部署到 GitHub Pages（免费 + 送二维码）

1. 打开 github.com → 右上角 **New repository**，名字建议 `eastseen`，选 **Public**，创建。
2. 进入仓库 → **Add file → Upload files**，把 `index.html` 拖进去 → **Commit changes**。
3. 仓库页 → **Settings → Pages**（左侧栏）→ **Branch** 选 `main` + `/ (root)` → **Save**。
4. 等一两分钟，页面顶部会出现网址，形如：
   `https://你的用户名.github.io/eastseen/`
5. 用手机访问这个网址一次——首页底部的二维码会**自动**变成这个网址，任何手机扫码就能进。

### 以后怎么更新（随时、免费）

- 方式 A：本地改完 `index.html`，回到仓库页 **Add file → Upload files** 覆盖上传，一分钟内生效。
- 方式 B：在 GitHub 网页上直接点开 `index.html` → 铅笔图标在线编辑 → Commit，最方便改文案。

## 三、日常改内容（不用懂代码）

用 PyCharm 打开 `index.html`，搜索 `const SITE = {`，改这一块就行：

```js
const SITE = {
  brand: "看得见的东方",
  email: "hello@eastseen.studio",   // ← 换成你的邮箱
  items: [                           // ← 画廊里的作品
    { name:"空山", en:"...", type:"pod", price:239, method:"肌理提取", desc:"...",
      art:{ seed:"空山新雨后", pal:"celadon", comp:"mountain" } },
    ...
  ]
};
```

- `type`：`digital` 数字文件 / `pod` 实物 / `bespoke` 定制
- `pal`：`celadon` 青绿 / `cinnabar` 朱砂 / `ink` 墨白 / `gold` 鎏金 / `indigo` 靛蓝
- `comp`：`mountain` 重峦 / `moon` 月轮 / `water` 一线水 / `forest` 密林
- `seed`：任意中文词，换一个词就换一张画（引擎按词生成，同一词结果稳定）

其余文案直接在 HTML 里搜索中文即可定位修改。

## 四、可选：接入 DeepSeek API

工坊默认用**本地命名引擎**（离线也能用）。如果想用 DeepSeek 生成作品名和英文文案：

1. 打开网站 → 滚动到「视觉工坊」→ 左栏底部点「配置 AI 命名」→ 粘贴你的 DeepSeek API Key。
   Key 只保存在你自己的浏览器 localStorage 里，**不会**被上传。
2. 保存后点「生成新作」，如果接口通畅，标题会自动换成 AI 起的名字。

> 注意：浏览器直连 `api.deepseek.com` 有时会因 CORS 被拦（页面会自动回退到本地命名，不影响使用）。想稳定走 AI 命名，可部署一个免费的 Cloudflare Worker 转发：

```js
// Cloudflare Worker（免费额度足够），部署后把地址替换代码里的 fetch 目标即可
export default {
  async fetch(req) {
    if (req.method === "OPTIONS") return new Response(null, { headers: {
      "Access-Control-Allow-Origin": "*", "Access-Control-Allow-Methods": "POST, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Authorization" } });
    const r = await fetch("https://api.deepseek.com/chat/completions", {
      method: "POST",
      headers: { "Content-Type": "application/json", "Authorization": req.headers.get("Authorization") },
      body: await req.text()
    });
    return new Response(await r.text(), { headers: {
      "Content-Type": "application/json", "Access-Control-Allow-Origin": "*" } });
  }
};
```

然后把 `index.html` 里 `fetch("https://api.deepseek.com/chat/completions"` 换成你的 Worker 地址。

## 五、这个网站有什么

| 模块 | 说明 |
|---|---|
| 水墨首屏 | Canvas 实时渲染的山峦、云雾与日/月，随滚动呼吸 |
| 序 · 逐字浮现 | 品牌宣言逐字显影 |
| 转译 · 三条产品线 | 苹果式锁定滚动，横向滑过数字/实物/定制三条线 |
| 视觉工坊 | 输入任意中文词 → 本地生成一张独版山水作品，可导出 SVG、一键上新到画廊 |
| 数字画廊 | 筛选、收藏（存浏览器本地）、灯箱详情、下载矢量文件 |
| 非遗数字方舟 | 书法/青花/苏绣/榫卯/年画的「转译方法」清单 |
| 引擎 | 环形指标、绿黄红三级管控、团队贡献点结算器 |
| 扫码带走 | 页面内置二维码编码器，自动指向当前网址，可保存 SVG |
| 其他 | 昼夜双主题、⌘K 命令面板、移动端适配、尊重系统「减弱动态效果」设置 |

## 六、常见问题

- **二维码显示"本地预览"？** 因为你还在用 `file://` 或 `localhost` 打开。部署到 GitHub Pages 后自动变为公网地址。
- **字体不一样？** 标题使用思源宋体（Noto Serif SC），首次加载需联网拉取；失败时自动回退到系统宋体，不影响排版。
- **想换主色？** 搜索 `--jade:#2E6B5B`，改这一个变量即可全局换色。
