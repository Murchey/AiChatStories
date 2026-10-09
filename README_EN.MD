# AiChat Stories English README

This is a static example repository for the AiChat story community. It can be published to a public GitHub repository, Gitee repository, COS/OSS bucket, or used as the content source for a self-hosted Spring Boot story service.

The repository follows this layout:

```text
index.json
assets/
  <story-id>/
    <version>.json
```

The test catalog contains four text-only settings: Mist Harbor Observatory (v2), Starbridge Archive, Night Ferry Radio, and Quiet City Protocol. They are useful for checking feed cards, local search, tag filters, version labels, story details, and character installation. After downloading a package, a user selects a local character in the app. The post introduction is displayed for reading, while memory points are installed as an independent “story memory” source. Updating or removing the story does not remove manually created memories.

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

1. Upload `index.json` and the `assets/` directory to your bucket.
2. Expose the files through a public read-only URL or signed URLs.
3. Choose COS / OSS in the app.
4. Enter the public bucket URL and `index.json`, or enter the complete URL of `index.json`.

Do not put COS SecretId, SecretKey, AccessKey, or SecretKey values in the app configuration. A public read-only directory is recommended.

### Story file format

`index.json` contains only feed metadata. The detail JSON is fetched after the user opens a card:

```json
{
  "schemaVersion": 2,
  "stories": [{
    "storyId": "mist-harbor-observatory",
    "version": 2,
    "title": "Mist Harbor Observatory",
    "author": "AiChat Example",
    "publishedAt": "2026-10-09T00:00:00Z",
    "summary": "Short summary",
    "tags": ["fantasy"],
    "file": "assets/mist-harbor-observatory/2.json"
  }]
}
```

A detail file uses `introduction` for the post body, `memories` for installable character memories, and optional `images` paths relative to the detail file:

```json
{
  "schemaVersion": 2,
  "storyId": "mist-harbor-observatory",
  "version": 2,
  "title": "Mist Harbor Observatory",
  "introduction": "Post body.",
  "memories": ["A setting fact"],
  "images": ["../img/cover.png"]
}
```

### Publish a new story

1. Add a version file under `assets/<story-id>/`, such as `2.json`.
2. Fill in `storyId`, `title`, and `introduction`; add `memories` and `images` as needed.
3. Keep image paths relative to the detail file and upload the image files.
4. Update the version, summary, tags, and `file` fields in `index.json`.
5. Push the repository or upload the updated files to object storage.

### COS / OSS setup

The static COS / OSS source only needs object reads; it does not require bucket listing permissions:

```text
{BASE_URL}/
├── index.json
└── assets/
    └── <story-id>/
        └── <version>.json
```

#### Tencent COS

1. Create a bucket with **public read / private write**.
2. Upload `index.json` and the `assets/` directory.
3. Note the endpoint, for example:

```text
https://<bucket>-<appid>.cos.<region>.myqcloud.com
```

4. In AiChat, open **Discover → Story Community → COS / OSS**, enter the endpoint, and use `index.json` as the index path. You can also enter the complete index URL.

For a private bucket, use signed URLs for `index.json` and story files. Never put SecretId, SecretKey, or AccessKey values in the repository or app configuration.

#### Aliyun OSS

1. Create a bucket with **public read** ACL.
2. Upload the same `index.json` and `assets/` layout.
3. Use an endpoint such as:

```text
https://<bucket>.oss-cn-hangzhou.aliyuncs.com
```

4. Enter the public endpoint and `index.json`, or the complete index URL, in AiChat.

For a prefix such as `/aichat`, store `aichat/index.json` and `aichat/assets/...`, then enter the prefix or the complete index URL.

#### Verify the bucket

```bash
curl -i "https://your-domain/index.json"
curl -I "https://your-domain/assets/<story-id>/<version>.json"
```

Both requests should return `200`. Native app requests normally do not need CORS. Publish only shareable story content; never upload API keys, user data, or backups.


Object storage layout / 对象存储目录：`stories/index.json` is the catalog; story details live under `stories/assets/<storyId>/<version>.json`; images use `stories/assets/img/` relative paths. Configure the App storage path as `stories`.
