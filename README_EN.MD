# AiChat Stories

## Purpose

This repository is a static story source for the AiChat Story Community. It uses a lightweight catalog plus on-demand details: the app loads the root `index.json` for the feed and downloads a detail JSON only after a user opens a post. The first batch contains five alternate-universe game-character romance stories about love, sacrifice, memories, and emotions. Each post labels its suggested character with “（建议角色：xxx）”; this is optional and can be adapted to another local character. The body includes character background, user setup, and plot. Memories are initial imported context only, with no requirement for the character to record new memories.

## Repository layout

```text
index.json                              # Root catalog loaded by the feed
assets/
  firefly-memory-core.json               # Detail JSON; one file per story
  yae-sakura-fragments.json
  black-swan-twin-star.json
  raiden-eternal-return.json
  kafka-emotional-thread.json
  img/                                  # Shared image directory
    <story-id>-cover.png
```

Every catalog `file` is relative to `index.json`, for example `assets/firefly-memory-core.json`. Every detail `images` entry is relative to its detail JSON. Because details are stored under `assets/` and images under `assets/img/`, use `img/<file>` in a detail file.

## GitHub and Gitee deployment

1. Keep `index.json` at the repository root and `assets/` beside it.
2. Commit the JSON and image files and push them to your chosen branch.
3. In AiChat, open **Discover → Story Community → Settings** and select GitHub or Gitee.
4. Enter `owner/repository`, the branch, and `index.json` as the index path.
5. Save and test the source.

The app tries Raw URLs first and falls back to web Raw and the Contents API. GitHub and Gitee do not use Secret ID or Secret Key. The repository and branch must allow the app to read `index.json` and the referenced `assets/` files.

## COS / OSS deployment

Use a dedicated object prefix such as `stories/`. The object keys should be:

```text
bucket/
└── stories/
    ├── index.json
    └── assets/
        ├── firefly-memory-core.json
        ├── yae-sakura-fragments.json
        ├── black-swan-twin-star.json
        ├── raiden-eternal-return.json
        ├── kafka-emotional-thread.json
        └── img/<story-id>-cover.png
```

Configure the app as follows:

- **Public object URL**: the HTTPS bucket endpoint, such as `https://bucket-123.cos.ap-guangzhou.myqcloud.com` or `https://bucket.oss-cn-hangzhou.aliyuncs.com`. Usually do not repeat `stories/` here.
- **Storage path**: enter `stories/`. The app requests `{public URL}/stories/index.json` and resolves detail files from the catalog.
- **Index path**: use `index.json` for the new layout. Older configurations containing a complete object key remain supported.
- **Secret ID / Secret Key**: leave them empty for public read. For a private bucket, enter the provider credentials; the app signs COS/OSS GET requests locally. Secrets stay in local configuration and are sanitized in data backups.

You may also enter the complete index URL, for example `https://example.com/stories/index.json`. In that case the URL determines the prefix. The settings page can copy the endpoint and credentials from a saved character-package object-storage configuration.

The source only needs reads for concrete objects; bucket listing permission is unnecessary. A private bucket should grant `GetObject` for:

```text
stories/index.json
stories/assets/*.json
stories/assets/img/*
```

Tencent COS endpoints look like `https://<bucket>-<appid>.cos.<region>.myqcloud.com`; Aliyun OSS endpoints look like `https://<bucket>.oss-cn-hangzhou.aliyuncs.com`. If the endpoint already contains a prefix such as `/aichat`, make sure the final URL does not duplicate that prefix.

## JSON formats

The catalog contains feed metadata only:

```json
{
  "stories": [{
    "storyId": "firefly-memory-core",
    "title": "Love Etched into the Core",
    "author": "AiChat Romance Group",
    "publishedAt": "2026-10-09T00:00:00Z",
    "summary": "Short summary",
    "tags": ["romance", "daily life"],
    "file": "assets/firefly-memory-core.json"
  }]
}
```

A detail file requires `storyId`, `title`, and `introduction`. `memories` is a string array imported once into the character selected by the user. `images` is optional. The introduction is rendered as the post body and is never automatically added to character memory. A failed image request does not hide the text.

```json
{
  "schemaVersion": 2,
  "storyId": "firefly-memory-core",
  "version": 1,
  "title": "Love Etched into the Core",
  "author": "AiChat Romance Group",
  "introduction": "The post body can contain multiple paragraphs.",
  "memories": ["A relationship setting", "A fact that should remain true"],
  "images": ["img/firefly-memory-core-cover.png"]
}
```

## Publishing and verification

The app's Story Source Settings → Quick Post Editor can also create and edit posts. The COS / OSS key needs read/write access to the index, detail JSON, and `assets/img/` objects; removing images additionally requires `DeleteObject`. Add Image uploads into `assets/img/`, inserts an `img/<generated filename>` relative path, and shows a preview. The X on a preview deletes the remote image and its path. For published posts, deletion immediately updates the detail JSON; newly added images and other edits are written when Publish / Save is pressed. Images uploaded for an unpublished draft already exist remotely; use X before returning to the list to remove unwanted uploads.

Blank lines can separate memory paragraphs; each nonempty line becomes one memory and blank lines are ignored.

1. Add `assets/<storyId>.json`; update that same file for subsequent edits instead of creating a directory per post.
2. Match the filename to `storyId`. The index contains list metadata only and does not need `schemaVersion` or `version`; detail files may retain a version.
3. Put images under `assets/img/` and reference them as `img/...`.
4. Add or update the catalog entry and verify that its `file` exists.
5. Strictly parse every JSON file and check every catalog path.
6. After upload, verify the objects:

```bash
curl -i "https://your-domain/stories/index.json"
curl -i "https://your-domain/stories/assets/firefly-memory-core.json"
curl -I "https://your-domain/stories/assets/img/firefly-memory-core-cover.png"
```

All requests should succeed. Never commit user data, backups, or unrelated credentials to this repository.
