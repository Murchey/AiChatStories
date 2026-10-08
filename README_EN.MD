# AiChat Stories English README

This is a static example repository for the AiChat story community. It can be published to a public GitHub repository, Gitee repository, COS/OSS bucket, or used as the content source for a self-hosted Spring Boot story service.

The repository follows this layout:

```text
index.json
stories/
  <story-id>/
    <version>.json
```

The test catalog contains four text-only settings: Mist Harbor Observatory (v2), Starbridge Archive, Night Ferry Radio, and Quiet City Protocol. They are useful for checking feed cards, local search, tag filters, version labels, story details, and character installation. After downloading a package, a user selects a local character in the app. The chapters are installed as an independent “story memory” source. Updating or removing the story does not remove manually created memories.

### Local test checklist

Publish the repository to a public GitHub/Gitee repository, or expose the root directory as a public read-only COS/OSS directory. In the app, open “Discover → Story Community → Settings” and configure the source. A successful load should show four feed cards:

- Filter with `科幻` or `悬疑` to find “Starbridge Archive”;
- Filter with `治愈` to find “Night Ferry Radio”;
- Search for `版本测试` to find “Mist Harbor Observatory” v2;
- Open any card to check chapter preview, download, and character installation.

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

### COS / OSS setup

The static COS / OSS source only needs object reads; it does not require bucket listing permissions:

```text
{BASE_URL}/
├── index.json
└── stories/
    └── <story-id>/
        └── <version>.json
```

#### Tencent COS

1. Create a bucket with **public read / private write**.
2. Upload `index.json` and the `stories/` directory.
3. Note the endpoint, for example:

```text
https://<bucket>-<appid>.cos.<region>.myqcloud.com
```

4. In AiChat, open **Discover → Story Community → COS / OSS**, enter the endpoint, and use `index.json` as the index path. You can also enter the complete index URL.

For a private bucket, use signed URLs for `index.json` and story files. Never put SecretId, SecretKey, or AccessKey values in the repository or app configuration.

#### Aliyun OSS

1. Create a bucket with **public read** ACL.
2. Upload the same `index.json` and `stories/` layout.
3. Use an endpoint such as:

```text
https://<bucket>.oss-cn-hangzhou.aliyuncs.com
```

4. Enter the public endpoint and `index.json`, or the complete index URL, in AiChat.

For a prefix such as `/aichat`, store `aichat/index.json` and `aichat/stories/...`, then enter the prefix or the complete index URL.

#### Verify the bucket

```bash
curl -i "https://your-domain/index.json"
curl -I "https://your-domain/stories/<story-id>/<version>.json"
```

Both requests should return `200`. Native app requests normally do not need CORS. Publish only shareable story content; never upload API keys, user data, or backups.
