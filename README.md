# TVBox 固定配置地址

把下面的**固定地址**填进电视 TVBox / 影视仓的「配置地址」里，以后只维护本仓库，电视端再也不用改地址。

## 电视里填哪个

按顺序试，哪个能打开首页内容就用哪个：

```
https://cdn.jsdelivr.net/gh/testtestqq/tvbox-config@main/tvbox.json
https://fastly.jsdelivr.net/gh/testtestqq/tvbox-config@main/tvbox.json
https://ghfast.top/https://raw.githubusercontent.com/testtestqq/tvbox-config/main/tvbox.json
https://raw.githubusercontent.com/testtestqq/tvbox-config/main/tvbox.json
```

第 4 个是 GitHub 原始地址，境内网络大多访问不了，所以优先用前面几个 CDN 地址。地址里的 `@main` 指向主分支，**永远不会变**。

## 以后源失效了怎么办

**只改 `SOURCE_URL` 这一个文件**，用手机浏览器打开 GitHub 也能改：

1. 打开 `SOURCE_URL`，把新接口地址粘进去（一行一个）。
2. 建议保留 2-3 个源：脚本按顺序尝试，第一个返回有效 JSON 的生效，主源挂了会自动切到备用源，连改都不用改。
3. 保存（Commit changes）后会自动触发同步；也可以进 Actions → Sync TVBox config → Run workflow 手动跑一次。
4. 等 1-2 分钟，电视上退出重进 TVBox 即可。

**电视里的地址一次都不用动。**

## 它做了什么

每 6 小时（以及每次改 `SOURCE_URL` 时），GitHub Actions 会：

- 依次请求 `SOURCE_URL` 里的地址，校验返回的是合法 TVBox JSON（含 `sites`/`lives`/`spider`）；
- 把第一个有效的配置**原样**写成仓库里的 `tvbox.json`，不重排、不改写，避免破坏相对引用和 md5 校验；
- 全部源都挂时**保留上一版** `tvbox.json`，不会把电视刷成空白；
- 在 `last-check.txt` 记录日期与状态（`OK` / `FAILED`），这个文件同时保证仓库持续有活动，避免 GitHub 自动停用定时任务；
- 推送后刷新 jsDelivr 缓存。

看同步是否正常：打开仓库里的 `last-check.txt`，日期是今天且状态 `OK` 就没事；`FAILED` 说明所有备用源都挂了，该补新源了。

## 注意

- 仓库必须是 **Public**，否则电视盒子无法免鉴权读取 raw 文件。仓库里只有配置 JSON，请勿放任何个人凭据。
- 配置里 `spider`、`lives`、`sites.ext` 都是绝对 URL，所以镜像到本仓库不会断链；如果以后换成含 `./` 相对路径的接口，需要在 JSON 里把它们改写成绝对地址。
- CDN 可能有几分钟缓存，换源后如果电视没立刻变，退出重进一次 TVBox，或稍等片刻。
