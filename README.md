# n8n Zalo + Facebook content bot

A set of **n8n** workflows that automate a content pipeline: an AI writer (Gemini) or self-written posts → review and edit inside a **Zalo group** with chat commands → publish to a **Facebook Page** (multi-image album) and a **Zalo OA article**, with hidden preview and scheduling.

Chat commands and some field names are in Vietnamese (the bot is used by a Vietnamese team); this README explains them.

## Workflows

| Workflow | What it does | Nodes | File |
|---|---|---|---|
| Zalo OA - Group Content Bot (GMF) | Zalo group bot: receives commands, images and video links and routes them to sub-workflows; remembers sent images for 15 minutes (Data Table) | 66 | [zalo-group-content-bot.json](workflows/zalo-group-content-bot.json) |
| AI writer from Sheet titles (pending approval) | Gemini writes a post from a title in Google Sheets → pending-approval row → sent to the group | 41 | [ai-writer-from-sheet-titles.json](workflows/ai-writer-from-sheet-titles.json) |
| Approve posts from Zalo | `/duyet`, `/bo`, `/hengio`, `/choduyet` | 8 | [approve-posts.json](workflows/approve-posts.json) |
| Edit posts from Zalo | `/sua` (AI), `/dungbai`, `/anh`, `/lichsu`, `/xem`, `/hoantac` – every version kept | 64 | [edit-posts.json](workflows/edit-posts.json) |
| Preview & publish | `/xemtruoc` posts HIDDEN to Fanpage + Zalo OA; `/congkhai` publishes now or at a set time | 35 | [preview-and-publish.json](workflows/preview-and-publish.json) |
| Auto-post to Facebook Page | Posts scheduled rows to the Facebook Page (multi-image album) at the set time, reminders at 17:00/19:00 | 31 | [auto-post-facebook-page.json](workflows/auto-post-facebook-page.json) |
| Cross-posting (Zalo OA + group) | Publishes the Zalo OA article (AI places images, `[ảnh N]` markers) and notifies the group | 43 | [cross-posting.json](workflows/cross-posting.json) |
| Set image set | `/datanh` – sets the whole image set (square = main image, landscape = cover) | 23 | [set-image-set.json](workflows/set-image-set.json) |
| Use self-designed image | `/dunganh` – use one self-made image as main image / cover | 26 | [use-own-image.json](workflows/use-own-image.json) |
| Add images to post | `/themanh`, `/xoaanh`, `/xepanh`, `/xemanh` | 23 | [add-images.json](workflows/add-images.json) |
| Zalo OA cover image | `/anhbia` – AI re-lays the main image as a 16:9 cover | 32 | [zalo-oa-cover-image.json](workflows/zalo-oa-cover-image.json) |
| Preview grid image | Sub-workflow: merges several images into one numbered grid image | 9 | [preview-grid-image.json](workflows/preview-grid-image.json) |
| Duplicate image filter | Sub-workflow: compares image content (dHash) to drop repeated images | 6 | [duplicate-image-filter.json](workflows/duplicate-image-filter.json) |
| Zalo OA - Token | Refreshes the Zalo OA access token | 14 | [zalo-oa-token.json](workflows/zalo-oa-token.json) |
| Error alerts to Zalo | Error workflow: posts system errors to the group | 5 | [error-alerts.json](workflows/error-alerts.json) |
| Zalo OA - Ads Image | `/ads` – ad image + copy | 26 | [zalo-oa-ads-image.json](workflows/zalo-oa-ads-image.json) |
| Facebook Ads metrics to Sheet | Pulls Facebook Ads metrics into Google Sheets (inactive) | 16 | [facebook-ads-metrics.json](workflows/facebook-ads-metrics.json) |

## Flow

1. A post is created: AI writes it from a Sheet title (`/tieude`, daily 8:00), from a YouTube/TikTok link, or it is pasted with `/baimoi`.
2. The post becomes a **Chờ duyệt** (pending approval) row in Google Sheets and is sent to the Zalo group.
3. The team adjusts text and images with commands; every change is saved to an edit-history tab.
4. `/xemtruoc` posts a hidden preview; `/congkhai` publishes it (now or at a time). Or `/duyet` schedules a normal post (default 19:30).
5. Auto-post publishes the Facebook Page post, then Cross-posting publishes the Zalo OA article and notifies the group.

## Zalo group commands

`<id>` = post number returned by the bot, `[ ]` = optional.

| Group | Command | Meaning |
|---|---|---|
| Create | `/baimoi` + new line + post | Import a self-written post (no AI); first line = title, second line = description |
| Create | `/tieude <title>` · `/dstieude` | AI writes a post now · list queued titles |
| Create | YouTube / TikTok link | AI writes a post from the video transcript (no image) |
| Images | `/datanh <id>` | Replace the whole image set with the images just sent (first square = main, first landscape = cover) |
| Images | `/xemanh <id>` · `/xepanh <id> <order>` | View the numbered image set · reorder (first number = new main image) |
| Images | `/themanh <id> [fb\|zalo]` · `/xoaanh <id> <n\|het>` | Add images (optionally one channel only) · remove an image / all extras |
| Images | `/dunganh <id> [fb\|zalo]` | Use one self-made image (square = main, landscape = cover) |
| Images | `/anh <id> [ngang] [request]` · `/anhbia <id>` | AI redraws the image · AI makes a 16:9 Zalo OA cover |
| Images | `[ảnh N]` line in the post | Put image N exactly there in the Zalo OA article (removed on Facebook) |
| Text | `/dungbai <id>` + new line + post · `/sua <id> <request>` | Replace the whole text · AI edit |
| History | `/lichsu <id> [chu\|anh\|bia]` · `/xem <id> chu\|anh\|bia <v>` · `/hoantac <id> [...] [v]` | List versions · show a version · undo / go back to a version |
| Publish | `/xemtruoc <id> [time]` · `/congkhai <id> [time\|huy]` | Hidden preview · publish now / schedule / cancel schedule |
| Publish | `/duyet <id> [time]` · `/hengio <id> <time>` · `/bo <id>` · `/choduyet` | Approve (default 19:30) · change time · drop · list pending posts |
| Other | `/ads <description>` | Ad image + copy |

Times: `20:15`, `20h`, `9h30 mai` (tomorrow), `8:00 16/11`. Images sent to the group are remembered for 15 minutes.

## Data

- Google Sheet "schedule": `ID | Tiêu đề (title) | Nội dung (content) | Link ảnh (main image) | Thời gian đăng (post time) | Trạng thái (status) | Post ID | Link bài (post link) | Ghi chú (notes) | Ảnh bìa OA (OA cover) | Ảnh thêm (extra images) | Ảnh Zalo (Zalo-only main image)`, plus an edit-history tab.
- Statuses: `Chờ viết` (to write) → `Chờ duyệt` (pending approval) → `Chờ đăng` (scheduled) / `Chờ công khai` (waiting to publish) → `Đã đăng` (posted); `Đã bỏ` (dropped), `Lỗi` (error).
- n8n Data Tables: Zalo OA tokens, pending images.

## Removed before publishing

- Tokens, API keys and secrets (Facebook, Zalo, Google…) → `<REDACTED>`; n8n credential IDs (names kept).
- Google Sheet, Zalo group, Zalo OA, Facebook Page and Data Table IDs, Zalo user IDs, n8n host, webhook paths → `<...>` placeholders.
- Test data (pinData) and staticData.

## Import into n8n

1. **Import from File** each file in `workflows/`.
2. Re-select credentials (Facebook Graph API, Google Sheets, Google Gemini) and replace the `<...>` placeholders with your own IDs.
3. In every *Execute Workflow* node, re-select the sub-workflow; publish the sub-workflows first, then the GMF bot.
