# AiChat Stories

## 中文说明

这是 AiChat 故事线社区的静态内容示例仓库。它可以直接部署到公开的 GitHub、Gitee、COS 或 OSS 存储中，也可以作为自建 Spring Boot 故事服务的数据源。

仓库约定：

```text
index.json
stories/
  <story-id>/
    <version>.json
```

当前测试目录包含四个文字故事设定：雾港观测站（v2）、星桥档案馆、夜航电台和静默城市协议。它们用于验证信息流卡片、搜索、标签筛选、版本展示、故事详情和角色安装。安装后，用户会在 App 中选择一个本地角色，故事章节会作为独立的“故事线记忆”加入角色记忆池。更新或卸载故事时，不会删除用户手动添加的记忆。

### 本地测试清单

将仓库推送到公开 GitHub/Gitee 仓库，或把根目录作为 COS/OSS 公共只读目录，然后在 App 的“发现 → 故事线社区 → 设置”中配置来源。加载成功后应看到四张帖子卡片：

- 使用“科幻”“悬疑”标签筛选“星桥档案馆”；
- 使用“治愈”标签筛选“夜航电台”；
- 搜索“版本测试”查看“雾港观测站”v2；
- 打开任意卡片，检查章节预览、下载和角色安装流程。

### 在 App 中配置

#### GitHub / Gitee

1. 将本仓库推送到自己的 GitHub 或 Gitee 仓库。
2. 在 AiChat 的“发现 → 故事线社区”中选择 GitHub 或 Gitee。
3. 填写仓库地址，例如 `owner/repository`。
4. 分支填写 `main`，索引路径填写 `index.json`。
5. 点击“保存并测试来源”。

App 会读取公开 Raw 文件，不需要填写 GitHub Token 或 Gitee Token。

#### COS / OSS

1. 将 `index.json` 和 `stories/` 上传到对象存储桶。
2. 为这些文件提供公共只读地址，或配置可以读取文件的预签名 URL。
3. 在 App 中选择 COS / OSS。
4. 填写对象存储公共地址和索引路径 `index.json`，或直接填写完整的 `index.json` 地址。

不要把 COS SecretId、SecretKey、AccessKey 或 SecretKey 写入 App 配置。推荐使用公共只读目录。

COS / OSS 故事目录只需要读取对象，不需要开放对象列表权限：

```text
{BASE_URL}/
├── index.json
└── stories/
    └── <story-id>/
        └── <version>.json
```

#### 腾讯云 COS 设置

1. 创建 Bucket，建议访问权限选择“公有读、私有写”。
2. 上传 `index.json` 和 `stories/` 目录。
3. 记录访问域名，例如：

```text
https://<bucket>-<appid>.cos.<region>.myqcloud.com
```

4. 在 AiChat 的“发现 → 故事线社区 → COS / OSS”中填写访问域名，索引路径填写 `index.json`；也可以直接填写完整索引地址。

如果 Bucket 必须保持私有，请为 `index.json` 和故事文件生成有效的预签名 URL，并直接填写完整索引地址。不要将 SecretId、SecretKey 或 AccessKey 写入仓库或 App 配置。

#### 阿里云 OSS 设置

1. 创建 Bucket，建议 ACL 使用“公共读”。
2. 上传相同的 `index.json` 和 `stories/` 目录。
3. 记录访问域名，例如：

```text
https://<bucket>.oss-cn-hangzhou.aliyuncs.com
```

4. 在 AiChat 中填写公共地址和 `index.json`，或填写完整索引 URL。

如果使用路径前缀，例如 `/aichat`，对象应位于 `aichat/index.json` 和 `aichat/stories/...`，并在 App 中填写对应前缀或完整索引 URL。

#### COS / OSS 自检

```bash
curl -i "https://你的域名/index.json"
curl -I "https://你的域名/stories/<story-id>/<version>.json"
```

两个请求都应返回 `200`。AiChat 使用原生 HTTP 请求，通常不需要配置 CORS。对象存储中只放可公开分享的故事内容，不要上传 API Key、用户数据或备份文件。

### 发布新故事

1. 在 `stories/<story-id>/` 下新增版本文件，例如 `2.json`。
2. 确保新文件中的 `storyId` 与目录中的 ID 一致。
3. 在 `index.json` 中将该故事的 `version` 和 `file` 更新到最新版本。
4. 推送仓库或上传对象存储文件。

第一版故事包只支持文字、章节和记忆点，不支持图片、音频、评论、点赞或评分。

[ENGLISH VERSION::README_EN.MD](README_EN.MD)
