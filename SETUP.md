# 建立 APK 分发仓库 —— 操作步骤

本目录（`docs/apk-repo/`）里已经准备好一个**只分发 APK、不含源码**的仓库全部文件：

```
apk-repo/
├── index.html                            # GitHub Pages 落地页（SEO 主力，内容可被索引）
├── README.md                             # 仓库首页说明（GitHub 仓库页也会被索引）
└── .github/workflows/verify-release.yml  # 发版后自动核验 APK 是否可下载、页面数字是否对得上
```

`index.html` 与 `README.md` 里的版本号（v0.72）、体积（4.0 MB）、更新日志都已按当前版本填好，
所有 URL 已按你的账号 `Miruko2` 填好：仓库地址 <https://github.com/Miruko2/hanakos-android>。

---

## 第一步：在 GitHub 上建仓库 —— ✅ 已完成

仓库已建好：<https://github.com/Miruko2/hanakos-android>（Public、空仓库、未勾 Add README）。

> 如需重建：<https://github.com/new> → Repository name 填 `hanakos-android` →
> Description 填 `萤火虫之国（Hanakos）安卓 App 安装包发布仓库` → 选 **Public** → 点 Create repository。
> ⚠️ 必须 Public，私有仓库 Google 索引不到，等于没做 SEO。

## 第二步：把文件推上去

在本机执行（Git Bash）：

```bash
cd "/i/next-template-main_2026_05_26/next-template-main_2026_5_29/next-template-main/next-template-main/docs/apk-repo"

git init
git add .
git commit -m "发布页与说明"
git branch -M main
git remote add origin https://github.com/Miruko2/hanakos-android.git
```

⚠️ 本机 `git push` 可能需要带代理（见项目笔记，可用端口 `127.0.0.1:10808`）：

```bash
git -c http.proxy=http://127.0.0.1:10808 -c https.proxy=http://127.0.0.1:10808 push -u origin main
```

> 注意：`docs/apk-repo/` 在主项目仓库里是**未跟踪目录**（主项目 `.gitignore` 会把它当独立仓库处理，
> 因为里面有自己 `git init` 出来的 `.git`）。推完可以把 `.git` 留着，也可以复制一份到项目外再推。

## 第三步：上传 APK 到 Release

1. 打开 <https://github.com/Miruko2/hanakos-android/releases/new>
2. **Choose a tag** 填 `v0.72`，点 "Create new tag"
3. **Release title** 填 `v0.72`
4. 描述里贴更新日志：
   ```
   ### v0.72（versionCode 9）
   - 个人主页：关注后就地变成绿底「私信」，少绕一层
   - 个人主页：能把一条帖子置顶到「帖子」栏最前
   - 迷你播放器的封面下缘多了一条跟着响的波形
   ```
5. **Attach binaries** 里拖入本机的 APK：
   `<项目路径>/android-glass-demo/app/build/outputs/apk/release/app-release.apk`
6. 点 **Publish release**

发布后 `verify-release.yml` 会自动跑一遍，核验 APK 能不能下、页面数字对不对。

## 第四步：开启 GitHub Pages

1. 仓库 → **Settings** → 左侧 **Pages**
2. **Source** 选 `Deploy from a branch`
3. **Branch** 选 `main`，目录选 `/ (root)`，点 **Save**
4. 等一两分钟，落地页地址是：
   <https://miruko2.github.io/hanakos-android/>

这就是那个能被 Google 索引的落地页（`index.html` 里的 `canonical` / `og:url` 已指向它）。

## 第五步：让落地页快点被收录

1. 去 [Google Search Console](https://search.google.com/search-console) 添加资源
   `https://miruko2.github.io/hanakos-android/`（或整个 `github.io` 域）
2. 用「网址检查」提交这个 URL，请求编入索引
3. 去[百度搜索资源平台](https://ziyuan.baidu.com)同样提交一次

> GitHub Pages 域名权重很高，通常几天内就会被收录。
> ⚠️ GitHub Pages 的域名**大小写不敏感但显示时统一小写**，`miruko2.github.io` 与
> `Miruko2.github.io` 都能访问；`canonical` 里写小写是标准做法。

---

## 以后发新版怎么做

1. 按 `android-glass-demo/deploy/APP_UPDATE_GUIDE.md` 正常发版（改 versionCode、出包、传 R2）
2. 在这个仓库建一个新的 Release（tag `v0.73`），把新 APK 传上去
3. 改 `index.html` 和 `README.md` 里的三处数字：版本号、体积、更新日志
4. push

第 3 步的核验工作流会提醒你有没有漏改。

---

## 几个说明

**为什么选 GitHub 而不是上传应用商店？**
GitHub 页面本身权重高、能被 Google 索引，仓库页 + Pages 落地页等于多两个高权重入口；
应用商店的页面属于商店自己，你拿不到那份 SEO 权重。两者不冲突，以后想上商店也可以。

**落地页里的 SEO 要点**（已按这些写好）：
- `title` 与 `description` 都含「萤火虫之国 + 安卓/APK」这类搜索词
- 正文有版本号、体积、系统要求 —— 这些是搜索引擎判断页面有没有用的实信息
- 两份结构化数据：`SoftwareApplication` + `FAQPage`
- `canonical` 指向自己，避免和主站下载页互相稀释
- 页脚明确写了「与日本鸟取县景点、台湾萤火虫景点无关」——
  这句很重要：搜「萤火虫之国」的裸词被那些观光景点占满，
  明确撇清关系有助于 Google 把"社区/App"这个意图和你的站绑在一起

**和主站 `/download` 页的关系**：
两个页面都指向同一个 APK，不冲突。主站下载页走的是 `forum.hanakos.cc/download`，
这个是 GitHub 上的独立入口 —— 多一个外链来源，对品牌词也是正向信号。
