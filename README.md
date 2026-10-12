# AiChat Stories

*English version README: [README_EN.MD](https://github.com/Murchey/AiChatStories/blob/main/README_EN.MD)*

## 仓库用途

这是 AiChat 故事线社区的静态故事仓库示例。仓库采用“轻量索引 + 按需详情”设计：发现页只读取根目录 `index.json`，用户打开帖子后才读取对应的故事详情 JSON。当前发布内容为二游衍生及架空爱情故事。每篇以“（建议角色：xxx）”标注参考角色，可替换为其他本地角色，不要求绑定指定角色。正文保留角色背景、用户设定和具体剧情，记忆点仅用于首次导入，不要求角色主动记录新的记忆点。

## 仓库内文件结构

```text
index.json                              # 根索引，发现页首先读取
assets/
  firefly-memory-core.json               # 每篇故事一个 JSON，编号作为文件名
  yae-sakura-fragments.json
  black-swan-twin-star.json
  raiden-eternal-return.json
  kafka-emotional-thread.json
  img/                                  # 公共图片目录
    <story-id>-cover.png
```

`index.json` 中的 `file` 必须是相对于 `index.json` 的路径，例如 `assets/firefly-memory-core.json`。详情 JSON 中的 `images` 必须是相对于详情 JSON 的路径；详情文件位于 `assets/` 时，图片放在公共目录 `assets/img/`，应填写 `img/xxx.png`。

## 部署到 GitHub / Gitee

1. 保持 `index.json` 位于仓库根目录，`assets/` 与它同级。
2. 提交全部 JSON 和图片文件，并推送到 `main`（或你实际使用的分支）。
3. 在 App 的“发现 → 故事线社区 → 设置”中选择 GitHub 或 Gitee。
4. 填写仓库地址 `owner/repository`、分支和索引路径 `index.json`。
5. 保存并测试来源。

App 会先访问 Raw 地址；Raw 不可用时会尝试网页 Raw 和 Contents API。GitHub/Gitee 不需要填写 Secret ID 或 Secret Key。仓库公开性和分支权限必须允许读取 `index.json` 与 `assets/` 下的文件。

## AI辅助创作提示词建议

使用AI创作故事线的时候，你可以使用下面的提示词：

```
创作{角色名}故事线设定，可参考资料（"AiChatStories\DevelopDocs\写作要求V2.md"，{角色wiki网址（可选）/角色包本地地址（可选、建议填写）}，"D:\Projects\vibecoding\AiChatStories\README.md"），关于该故事线设定我的想法是：{填写你的初步想法（建议）}，完成之后同步更新index.json索引
```

当前公开故事索引地址：[index.json](https://raw.githubusercontent.com/Murchey/AiChatStories/main/index.json)

## 部署到 COS / OSS

对象存储中建议使用一个独立前缀，例如 `stories/`。最终对象键应为：

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
stories/assets/*.json
stories/assets/img/*
```

腾讯 COS 示例：`https://<bucket>-<appid>.cos.<region>.myqcloud.com`。阿里 OSS 示例：`https://<bucket>.oss-cn-hangzhou.aliyuncs.com`。如果域名本身已经包含 `/aichat` 等前缀，应将该前缀作为公共地址的一部分，并确认最终 URL 不会重复拼接目录。

## 文件格式

索引只存发现页所需字段：

```json
{
  "stories": [{
    "storyId": "firefly-memory-core",
    "title": "刻进核心的爱",
    "author": "AiChat 情感故事组",
    "publishedAt": "2026-10-09T00:00:00Z",
    "summary": "短摘要",
    "tags": ["恋爱", "日常"],
    "file": "assets/firefly-memory-core.json"
  }]
}
```

详情文件必填 `storyId`、`title`、`introduction`；`memories` 是字符串数组，首次导入时写入用户选择的角色；`images` 是可选图片路径。`introduction` 只作为帖子正文展示，不会自动写入角色记忆。图片加载失败时，正文仍会正常显示。

```json
{
  "schemaVersion": 2,
  "storyId": "firefly-memory-core",
  "version": 1,
  "title": "刻进核心的爱",
  "author": "AiChat 情感故事组",
  "introduction": "帖子正文，可以包含多段文字。",
  "memories": ["角色关系设定", "需要持续遵守的事实"],
  "images": ["img/firefly-memory-core-cover.png"]
}
```

## 标签规范

标签用于发现页筛选，不是角色背景或正文关键词。全仓库所有 `index*.json` 与详情 JSON 合计使用的去重标签类型不得超过 **30 个**；每篇故事使用 3—5 个标签，最多 6 个。总类型数达到上限后，应优先合并同义标签，而不是继续增加近义词。

每篇标签按“情感/关系 → 作品来源 → 核心题材”排序。至少包含 `恋爱`；百合向内容必须包含 `百合向`；能明确对应作品时补充作品名，再选 1—3 个真正影响检索的题材。不要写角色名、作者名、版本号、故事标题词、单次剧情词，也不要混用近义词，例如“校园/学院/现代学院”应统一为 `学院`。

当前统一标签表：

- 情感与关系：`恋爱`、`百合向`
- 作品来源：`鸣潮`、`崩坏：星穹铁道`
- 题材与场景：`学院`、`学术`、`偶像`、`同人创作`、`西幻`、`旅途`、`日常`、`成长`、`权力关系`

JSON 中的标签值始终使用上述中文规范值。新增标签前，先检查当前去重总数是否低于 30；能归入现有类型时不要新增。发布前同时校验详情与索引中的标签数组完全一致。

## 发布和自检流程

也可以在 App 的“故事线设置 → 帖子快捷编辑”中创建或编辑帖子。此功能需要当前 COS / OSS 密钥具备索引、详情 JSON 和 `assets/img/` 图片对象的读写权限；删除图片还需要 `DeleteObject` 权限。点击“添加图片”后，App 将图片上传到 `assets/img/`，自动填写 `img/<自动生成的文件名>` 并显示预览；预览右上角的 X 会删除远端图片和对应路径。编辑已发布帖子时，图片删除会立即同步到详情 JSON；新增图片和其他正文修改在点击“发布 / 保存”后写入。未发布的新帖图片上传后已存在于远端，可在返回列表前用 X 删除不需要的图片。

记忆点输入支持用空行分隔段落；每个非空行作为一条记忆点，空行不会导入。

1. 在 `assets/` 新增 `<storyId>.json`；同一篇帖子更新时修改原文件，不建立单独子目录。
2. 保证文件名与 `storyId` 一致，索引只保存列表元数据，不需要 `schemaVersion` 或 `version`；详情可保留自己的版本号。
3. 将图片放入 `assets/img/`，在详情中使用 `img/...` 引用。
4. 在 `index.json` 增加或更新对应条目，确认 `file` 路径真实存在。
5. 严格解析全部 JSON，并检查索引中的每个 `file` 都能找到文件。
6. 上传后检查：

```bash
curl -i "https://你的域名/stories/index.json"
curl -i "https://你的域名/stories/assets/firefly-memory-core.json"
curl -I "https://你的域名/stories/assets/img/firefly-memory-core-cover.png"
```

三个请求应返回成功状态。不要把用户数据、备份文件或无关密钥提交到故事仓库。




## 分索引规范（每页最多 100 条）

COS/OSS、GitHub、Gitee 使用相同规范。根索引既包含首页故事，也包含索引目录。索引目录是轻量文件列表，不嵌入其他页的故事；首次进入只请求根索引。当前故事不足 100 条，只列出根索引，不创建空分页。

```json
{
  "indexes": [
    {"file": "index.json", "title": "最新故事"},
    {"file": "index-2.json", "title": "第 2 页"}
  ],
  "stories": [
    {"storyId": "example", "title": "示例", "summary": "摘要", "tags": ["恋爱"], "file": "assets/example.json"}
  ]
}
```

后续文件如 `index-2.json` 只需 `{"stories": [...]}`。所有索引必须与根索引同目录；目录第一项必须是实际根索引文件名，文件名唯一，只允许英文、数字、下划线、短横线、点号和 `.json` 后缀，不能包含目录、URL、查询参数或 `..` 路径。`title` 用于 App 的索引选择菜单，省略时使用文件名。目录顺序就是“加载更多”的顺序。

每份新格式索引最多 100 条，所有索引中的 `storyId` 必须全局唯一。详情 `file` 始终相对于根索引，而非当前页；详情图片仍相对于详情 JSON。推荐结构：

```text
stories/
  index.json
  index-2.json
  index-3.json
  assets/
    <storyId>.json
    img/<图片文件名>.png
```

不需要 `schemaVersion` 或 `version`。没有 `indexes` 的旧索引仍可读取，App 不会截断旧文件中超过 100 条的内容；迁移时请人工拆分或通过快捷发布自动分流。

### 浏览、搜索与缓存

- 索引菜单按需切换，只显示所选页，清空搜索和标签。
- 搜索与标签筛选只针对已加载的索引，不在后台下载所有页。
- “加载更多故事”读取下一页，追加并使用当前搜索条件过滤；没有匹配结果仍可继续加载。
- 清空搜索恢复已加载内容，不请求网络。标签来自已加载的完整内容。
- 五分钟内优先使用本地缓存；超过五分钟再次进入刷新根索引。右上角刷新强制更新当前已加载页及根目录。
- 缓存最多保留 5 个来源、合计 5 MiB，按最近使用淘汰。故事详情仍只在打开帖子时下载。

### 发布与对象存储权限

手工发布先上传详情与后续索引，最后更新根索引及其 `indexes`。将所有 `index*.json` 和 `assets/` 一起部署到同一仓库分支或存储前缀。私有 COS/OSS 密钥需允许 `GetObject` 读取 `stories/index*.json`；快捷编辑还需要相应索引的 `PutObject` 权限，以及详情、图片的读写权限。清理旧对象和失败恢复还需要 `DeleteObject`。不需要列举桶内容的权限。

快捷发布将新帖放入首页首位；第 101 条及以后顺序流入后续页，必要时创建 `index-N.json`。编辑或删除仅更新所属页，不自动整理空缺。编辑器支持按页选择，发布和修改 ID 会读取所有索引检查重复编号，因此管理操作的流量比普通浏览大。

多文件写入没有事务保证，请串行发布，不要同时用多个管理员编辑。App 会先保存详情及后续页，最后写根索引；失败时尝试恢复修改过的索引并保留编辑内容。如果提示恢复失败，先核对远端索引再重试。上传成功但未被索引引用的详情或图片可能需要手动清理。删除帖子先更新索引，再删除详情；详情清理失败不影响帖子从社区移除。

发布前严格解析所有 JSON，确认目录中的索引存在、每页不超过 100 条、全局 ID 唯一、所有 `file` 存在且文件名与 ID 一致。只列出已上传的页，不列出未来或空的占位页。

