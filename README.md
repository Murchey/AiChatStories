# AiChat Stories

## 中文说明

这是 AiChat 故事线社区的静态内容示例仓库。它可以直接部署到公开的 GitHub、Gitee、COS 或 OSS 存储中，也可以作为自建 Spring Boot 故事服务的数据源。

仓库约定：

```text
index.json
assets/
  <story-id>/
    <version>.json
```

当前测试目录包含四个文字故事设定：雾港观测站（v2）、星桥档案馆、夜航电台和静默城市协议。它们用于验证信息流卡片、搜索、标签筛选、版本展示、故事详情和角色安装。安装后，用户会在 App 中选择一个本地角色，故事帖子正文与记忆点分离：帖子正文用于阅读，记忆点作为独立的“故事线记忆”加入角色记忆池。更新或卸载故事时，不会删除用户手动添加的记忆。

### 本地测试清单

将仓库推送到公开 GitHub/Gitee 仓库，或把根目录作为 COS/OSS 公共只读目录，然后在 App 的“发现 → 故事线社区 → 设置”中配置来源。加载成功后应看到四张帖子卡片：

- 使用“科幻”“悬疑”标签筛选“星桥档案馆”；
- 使用“治愈”标签筛选“夜航电台”；
- 搜索“版本测试”查看“雾港观测站”v2；
- 打开任意卡片，检查帖子正文、图片、折叠记忆点和角色安装流程。

### 在 App 中配置

#### GitHub / Gitee

1. 将本仓库推送到自己的 GitHub 或 Gitee 仓库。
2. 在 AiChat 的“发现 → 故事线社区”中选择 GitHub 或 Gitee。
3. 填写仓库地址，例如 `owner/repository`。
4. 分支填写 `main`，索引路径填写 `index.json`。
5. 点击“保存并测试来源”。

App 会读取公开 Raw 文件，不需要填写 GitHub Token 或 Gitee Token。

#### COS / OSS

1. 将 `index.json` 和 `assets/` 上传到对象存储桶。
2. 为这些文件提供公共只读地址，或配置可以读取文件的预签名 URL。
3. 在 App 中选择 COS / OSS。
4. 填写对象存储公共地址和索引路径 `index.json`，或直接填写完整的 `index.json` 地址。

不要把 COS SecretId、SecretKey、AccessKey 或 SecretKey 写入 App 配置。推荐使用公共只读目录。

COS / OSS 故事目录只需要读取对象，不需要开放对象列表权限：

```text
{BASE_URL}/
├── index.json
└── assets/
    └── <story-id>/
        └── <version>.json
```

#### 腾讯云 COS 设置

1. 创建 Bucket，建议访问权限选择“公有读、私有写”。
2. 上传 `index.json` 和 `assets/` 目录。
3. 记录访问域名，例如：

```text
https://<bucket>-<appid>.cos.<region>.myqcloud.com
```

4. 在 AiChat 的“发现 → 故事线社区 → COS / OSS”中填写访问域名，索引路径填写 `index.json`；也可以直接填写完整索引地址。

如果 Bucket 必须保持私有，请为 `index.json` 和故事文件生成有效的预签名 URL，并直接填写完整索引地址。不要将 SecretId、SecretKey 或 AccessKey 写入仓库或 App 配置。

#### 阿里云 OSS 设置

1. 创建 Bucket，建议 ACL 使用“公共读”。
2. 上传相同的 `index.json` 和 `assets/` 目录。
3. 记录访问域名，例如：

```text
https://<bucket>.oss-cn-hangzhou.aliyuncs.com
```

4. 在 AiChat 中填写公共地址和 `index.json`，或填写完整索引 URL。

如果使用路径前缀，例如 `/aichat`，对象应位于 `aichat/index.json` 和 `aichat/assets/...`，并在 App 中填写对应前缀或完整索引 URL。

#### COS / OSS 自检

```bash
curl -i "https://你的域名/index.json"
curl -I "https://你的域名/assets/<story-id>/<version>.json"
```

两个请求都应返回 `200`。AiChat 使用原生 HTTP 请求，通常不需要配置 CORS。对象存储中只放可公开分享的故事内容，不要上传 API Key、用户数据或备份文件。

### 故事文件格式

`index.json` 只保存发现页需要的元数据：

```json
{
  "schemaVersion": 2,
  "stories": [{
    "storyId": "mist-harbor-observatory",
    "version": 2,
    "title": "雾港观测站",
    "author": "AiChat 示例作者",
    "publishedAt": "2026-10-09T00:00:00Z",
    "summary": "短摘要",
    "tags": ["现代幻想"],
    "file": "assets/mist-harbor-observatory/2.json"
  }]
}
```

详情 JSON 只在用户点击卡片后加载：

```json
{
  "schemaVersion": 2,
  "storyId": "mist-harbor-observatory",
  "version": 2,
  "title": "雾港观测站",
  "author": "AiChat 示例作者",
  "publishedAt": "2026-10-09T00:00:00Z",
  "summary": "短摘要",
  "tags": ["现代幻想"],
  "introduction": "帖子正文。",
  "memories": ["需要写入角色记忆的设定"],
  "images": ["../img/cover.png"]
}
```

`introduction` 用于帖子展示，`memories` 仅在用户安装故事时写入角色记忆。图片放在`assets/img/` 目录（详情 JSON 使用 `../img/...` 相对路径）中；图片读取失败不会阻塞正文。

### 发布新故事

1. 在 `assets/<story-id>/` 下新增版本文件，例如 `2.json`。
2. 在详情 JSON 中填写 `storyId`、`title`、`introduction`，并按需填写 `memories`、`images`。
3. 确保图片路径相对于详情 JSON，且文件已上传。
4. 在 `index.json` 中更新对应条目的版本、摘要、标签和 `file`。
5. 推送仓库或上传对象存储文件。

[ENGLISH VERSION::README_EN.MD](README_EN.MD)


Object storage layout / 对象存储目录：`stories/index.json` is the catalog; story details live under `stories/assets/<storyId>/<version>.json`; images use `stories/assets/img/` relative paths. Configure the App storage path as `stories`.
