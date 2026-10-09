# AiChat Stories

## 仓库用途

这是 AiChat 故事线社区的静态故事仓库示例。仓库采用“轻量索引 + 按需详情”设计：发现页只读取根目录 `index.json`，用户打开帖子后才读取对应的故事详情 JSON。当前示例包含五种恋爱情感方向：慢热日常、重逢治愈、异地思念、青春暗恋和成熟关系。

## 仓库内文件结构

```text
index.json                              # 根索引，发现页首先读取
assets/
  first-light-cafe/
    1.json                              # 故事详情，版本号作为文件名
  after-the-rain/
    1.json
  moonlit-distance/
    1.json
  unspoken-summer/
    1.json
  quiet-harbor/
    1.json
  img/                                  # 公共图片目录
    <story-id>-cover.png
```

`index.json` 中的 `file` 必须是相对于 `index.json` 的路径，例如 `assets/first-light-cafe/1.json`。详情 JSON 中的 `images` 必须是相对于详情 JSON 的路径；详情文件位于 `assets/<story-id>/` 时，图片放在公共目录 `assets/img/`，应填写 `../img/xxx.png`。

## 部署到 GitHub / Gitee

1. 保持 `index.json` 位于仓库根目录，`assets/` 与它同级。
2. 提交全部 JSON 和图片文件，并推送到 `main`（或你实际使用的分支）。
3. 在 App 的“发现 → 故事线社区 → 设置”中选择 GitHub 或 Gitee。
4. 填写仓库地址 `owner/repository`、分支和索引路径 `index.json`。
5. 保存并测试来源。

App 会先访问 Raw 地址；Raw 不可用时会尝试网页 Raw 和 Contents API。GitHub/Gitee 不需要填写 Secret ID 或 Secret Key。仓库公开性和分支权限必须允许读取 `index.json` 与 `assets/` 下的文件。

## 部署到 COS / OSS

对象存储中建议使用一个独立前缀，例如 `stories/`。最终对象键应为：

```text
bucket/
└── stories/
    ├── index.json
    └── assets/
        ├── first-light-cafe/1.json
        ├── after-the-rain/1.json
        ├── moonlit-distance/1.json
        ├── unspoken-summer/1.json
        ├── quiet-harbor/1.json
        └── img/
            └── <story-id>-cover.png
```

在 App 中填写：

- **对象存储公共地址**：桶的 HTTPS 访问域名，例如 `https://bucket-123.cos.ap-guangzhou.myqcloud.com` 或 `https://bucket.oss-cn-hangzhou.aliyuncs.com`；通常不要在这里重复填写 `stories/`。
- **存储桶路径**：填写 `stories/`。App 会请求 `{公共地址}/stories/index.json`，并按索引中的 `file` 拼接详情地址。
- **索引路径**：新格式填写 `index.json`。旧版本若保存了完整对象键，仍可继续使用旧路径。
- **Secret ID / Secret Key**：公共读时留空；私有读时填写对应云厂商密钥，App 会为 COS/OSS 的 GET 请求生成签名。密钥只保存在本机配置中，数据备份会自动脱敏。

也可以把“索引地址”直接填写为完整 URL，例如 `https://example.com/stories/index.json`。使用完整索引地址时，路径前缀由 URL 决定。配置页面支持从已保存的角色包对象存储配置同步地址和密钥。

对象存储只需要允许读取具体对象，不需要开放列举桶内容的权限。若使用私有桶，请确保密钥有 `GetObject` 权限，并允许读取：

```text
stories/index.json
stories/assets/**/*.json
stories/assets/img/*
```

腾讯 COS 示例：`https://<bucket>-<appid>.cos.<region>.myqcloud.com`。阿里 OSS 示例：`https://<bucket>.oss-cn-hangzhou.aliyuncs.com`。如果域名本身已经包含 `/aichat` 等前缀，应将该前缀作为公共地址的一部分，并确认最终 URL 不会重复拼接目录。

## 文件格式

索引只存发现页所需字段：

```json
{
  "schemaVersion": 2,
  "stories": [{
    "storyId": "first-light-cafe",
    "version": 1,
    "title": "晨光咖啡馆",
    "author": "AiChat 情感故事组",
    "publishedAt": "2026-10-09T00:00:00Z",
    "summary": "短摘要",
    "tags": ["恋爱", "日常"],
    "file": "assets/first-light-cafe/1.json"
  }]
}
```

详情文件必填 `storyId`、`title`、`introduction`；`memories` 是字符串数组，首次导入时写入用户选择的角色；`images` 是可选图片路径。`introduction` 只作为帖子正文展示，不会自动写入角色记忆。图片加载失败时，正文仍会正常显示。

```json
{
  "schemaVersion": 2,
  "storyId": "first-light-cafe",
  "version": 1,
  "title": "晨光咖啡馆",
  "author": "AiChat 情感故事组",
  "introduction": "帖子正文，可以包含多段文字。",
  "memories": ["角色关系设定", "需要持续遵守的事实"],
  "images": ["../img/first-light-cafe-cover.png"]
}
```

## 发布和自检流程

1. 在 `assets/<story-id>/` 新增版本文件，例如 `2.json`。
2. 保证详情中的 `storyId`、`version` 与目录和文件名一致。
3. 将图片放入 `assets/img/`，在详情中使用 `../img/...` 引用。
4. 在 `index.json` 增加或更新对应条目，确认 `file` 路径真实存在。
5. 严格解析全部 JSON，并检查索引中的每个 `file` 都能找到文件。
6. 上传后检查：

```bash
curl -i "https://你的域名/stories/index.json"
curl -i "https://你的域名/stories/assets/first-light-cafe/1.json"
curl -I "https://你的域名/stories/assets/img/first-light-cafe-cover.png"
```

三个请求应返回成功状态。不要把用户数据、备份文件或无关密钥提交到故事仓库。
