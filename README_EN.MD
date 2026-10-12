# AiChat Stories

*Chinese version README: [README.md](https://github.com/Murchey/AiChatStories/blob/main/README.md)*

## Purpose

This repository is a static story source for the AiChat Story Community. It uses a lightweight catalog plus on-demand details: the app loads the root `index.json` for the feed and downloads a detail JSON only after a user opens a post. The published content features game-character and alternate-universe romance stories. Each post labels its suggested character with “（建议角色：xxx）”; this is optional and can be adapted to another local character. The body includes character background, user setup, and plot. Memories are initial imported context only, with no requirement for the character to record new memories.

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

Current public story catalog URL: [index.json](https://raw.githubusercontent.com/Murchey/AiChatStories/main/index.json)

## Recommended AI-assisted writing prompt

When using AI to create a storyline profile, you can use the following prompt:

``text
Create a storyline profile for {character name}. You may refer to "AiChatStories\DevelopDocs\Writing Requirements V2.md" and {optional character wiki URL / optional local character-package path (recommended)，"D:\Projects\vibecoding\AiChatStories\README.md"}. My initial idea for this storyline is: {enter your initial idea (recommended)}. After completion, update the index.json catalog as well.
``
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

## Tag policy

Tags are feed filters, not character lore or full-text keywords. Across every `index*.json` and detail JSON, the repository must use no more than **30 unique tag values**. Use 3—5 tags per story, with a maximum of 6. When the repository reaches the limit, merge synonyms instead of adding near-duplicates.

Order tags as relationship, source, then theme. Every story includes `恋爱`. Yuri stories also include `百合向`. Add the source name when it is known, followed by 1—3 themes that materially improve discovery. Do not use character names, author names, version labels, title fragments, one-off plot details, or synonyms such as mixing `校园`, `学院`, and `现代学院`; use `学院`.

Current controlled vocabulary:

- Relationship: `恋爱`, `百合向`
- Source: `鸣潮`, `崩坏：星穹铁道`
- Theme and setting: `学院`, `学术`, `偶像`, `同人创作`, `西幻`, `旅途`, `日常`, `成长`, `权力关系`

Keep these exact Chinese values in JSON so filtering stays consistent across language documents. Before adding a tag, confirm that the repository-wide unique count is below 30 and reuse an existing category whenever possible. Before publishing, verify that detail and index tag arrays match exactly.

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




## Multi-index format (up to 100 stories per page)

COS/OSS, GitHub and Gitee share the same format. The root contains both the first page and a lightweight directory. Opening the community fetches only the root, not every page. With fewer than 100 stories, list only the root and do not create empty pages.

```json
{
  "indexes": [
    {"file": "index.json", "title": "Latest stories"},
    {"file": "index-2.json", "title": "Page 2"}
  ],
  "stories": [
    {"storyId": "example", "title": "Example", "summary": "Preview", "tags": ["Romance"], "file": "assets/example.json"}
  ]
}
```

Other pages contain only `{"stories": [...]}`. All index files are siblings of the root. The first directory entry must name the actual root; names must be unique JSON filenames using letters, digits, underscores, hyphens and dots, without paths, URLs or queries. Optional `title` defaults to the filename. Directory order controls load-more order. Each new-format page holds at most 100 entries; story IDs are globally unique. Detail `file` paths always resolve against the root index, while image paths resolve against the detail JSON. Keep `assets/<storyId>.json` and `assets/img/` shared across all pages. No `schemaVersion` or `version` is required. Legacy indexes without `indexes` remain readable without truncation, even above 100 entries.

### Browsing and caching

The index menu replaces the feed with the selected page and clears search and tags. Search and tags filter only loaded pages without downloading other indexes. Load more fetches the next page, appends entries and applies the active filter; it remains available even if a page has no matches. Clearing search restores loaded content without network access. Tags come from the full loaded content. Cached pages are reused within five minutes; reopening after five minutes refreshes the root. Manual refresh updates the root directory and currently loaded pages. Index caches are limited to five sources and 5 MiB total, evicted by last use. Details remain on-demand.

### Publishing and permissions

Upload details and additional pages first, then publish the root directory last. Deploy all `index*.json` files and `assets/` to the same repository branch or storage prefix. Private COS/OSS credentials need GetObject for `stories/index*.json`. Editing also needs PutObject for indexes and details/images, and DeleteObject for cleanup and rollback; bucket listing is not required.

Quick publishing inserts a new story at the front of the root. Overflow beyond 100 entries cascades into subsequent pages, creating `index-N.json` when necessary. Editing and deleting update the owning page without filling gaps. The editor offers page selection; publishing and ID changes read all indexes to check globally duplicate IDs, so administrative traffic is higher than browsing traffic.

Static storage has no multi-object transactions: publish serially and avoid concurrent administrators. The App writes details and additional pages before the root, attempts index rollback on failure and keeps the draft. If rollback fails, inspect remote indexes before retrying. Unreferenced uploaded details/images may need manual cleanup. Deletion updates the index before removing the detail; failed detail cleanup does not put the story back into the feed.

Validate every JSON, directory target, 100-entry limit, globally unique ID and detail path before publishing. Do not list missing, future or empty placeholder pages.

