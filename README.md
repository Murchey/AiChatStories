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

当前示例包含一个“雾港观测站”故事设定，故事内容只有文字。安装后，用户会在 App 中选择一个本地角色，故事章节会作为独立的“故事线记忆”加入角色记忆池。更新或卸载故事时，不会删除用户手动添加的记忆。

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

### 发布新故事

1. 在 `stories/<story-id>/` 下新增版本文件，例如 `2.json`。
2. 确保新文件中的 `storyId` 与目录中的 ID 一致。
3. 在 `index.json` 中将该故事的 `version` 和 `file` 更新到最新版本。
4. 推送仓库或上传对象存储文件。

第一版故事包只支持文字、章节和记忆点，不支持图片、音频、评论、点赞或评分。

## English

This is a static example repository for the AiChat story community. It can be published to a public GitHub repository, Gitee repository, COS/OSS bucket, or used as the content source for a self-hosted Spring Boot story service.

The repository follows this layout:

```text
index.json
stories/
  <story-id>/
    <version>.json
```

The sample contains one text-only world setting called “Mist Harbor Observatory”. After downloading it, a user selects a local character in the app. The chapters are installed as an independent “story memory” source. Updating or removing the story does not remove manually created memories.

### Configure it in the app

#### GitHub / Gitee

1. Push this repository to your own GitHub or Gitee repository.
2. In AiChat, open “Discover → Story Community” and choose GitHub or Gitee.
3. Enter the repository, for example `owner/repository`.
4. Use `main` as the branch and `index.json` as the index path.
5. Tap “Save and test source”.

The app reads public Raw files. No GitHub or Gitee token is required.

#### COS / OSS

1. Upload `index.json` and the `stories/` directory to your bucket.
2. Expose the files through a public read-only URL or signed URLs.
3. Choose COS / OSS in the app.
4. Enter the public bucket URL and `index.json`, or enter the complete URL of `index.json`.

Do not put COS SecretId, SecretKey, AccessKey, or SecretKey values in the app configuration. A public read-only directory is recommended.

### Publish a new story

1. Add a new version file under `stories/<story-id>/`, such as `2.json`.
2. Keep the `storyId` inside the file equal to the directory ID.
3. Update the story `version` and `file` fields in `index.json`.
4. Push the repository or upload the updated files to object storage.

The first version supports text, chapters, and memory points only. Images, audio, comments, likes, and ratings are not supported yet.
