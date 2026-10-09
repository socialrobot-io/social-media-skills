---
name: social-media-manager
description: >-
  Claude social media manager that actually publishes. Schedule, post, edit,
  reschedule, or delete social media posts on Instagram, Facebook, LinkedIn,
  X (Twitter), TikTok, Threads, Pinterest, Bluesky, and Mastodon through the
  SocialRobot MCP server (https://socialrobot.io/api/mcp) or REST API. Works as
  a LinkedIn MCP server, Instagram MCP server, TikTok MCP server, and so on from
  one connection, in Claude, Claude Code, ChatGPT, Cursor, Codex, Gemini CLI, or
  OpenClaw. Use when the user says "schedule this post", "post this to LinkedIn
  and Instagram", "cross-post", "publish an X or Threads thread", "move Friday's
  post", "what's scheduled this week", "why did my post fail", or "how did last
  week's posts do". Social publisher companion to strategy skills such as
  social-content: they decide what to say, this one gets it on the calendar.
  For planning a whole week use social-media-content-calendar; for image
  dimensions use social-media-image-sizes.
homepage: https://socialrobot.io/mcp
metadata: {"openclaw":{"emoji":"🤖","homepage":"https://socialrobot.io/mcp","primaryEnv":"SOCIALROBOT_API_KEY","requires":{"anyBins":["curl"]},"envVars":[{"name":"SOCIALROBOT_API_KEY","required":false,"description":"SocialRobot API key (starts with sk_). Needed for REST/curl, or for MCP clients without a browser OAuth flow. Not needed when the MCP server is already connected with OAuth."}]}}
---

# Social media manager (SocialRobot)

SocialRobot (socialrobot.io) is a hosted scheduler. One post can target nine
networks: Instagram, Facebook, LinkedIn, X (Twitter), TikTok, Threads,
Pinterest, Bluesky, Mastodon. SocialRobot's servers publish at the scheduled
time, so the chat can be closed. Not supported: YouTube, Reddit, Telegram,
Discord, DMs, comment replies, ads.

Division of labor: a strategy skill (for example Corey Haines'
`social-content`) or the user decides *what* to post. This skill turns it into
real scheduled posts and reports back with real numbers.

## 1. Connect once

Remote MCP server: `https://socialrobot.io/api/mcp` (Streamable HTTP). Auth is
OAuth in the browser, or an API key sent as `Authorization: Bearer sk_...`.
Included on every plan, Free too.

```bash
# Claude Code
claude mcp add --transport http socialrobot https://socialrobot.io/api/mcp
# Codex
codex mcp add socialrobot --url https://socialrobot.io/api/mcp
# Gemini CLI (then run /mcp auth socialrobot)
gemini mcp add --transport http socialrobot https://socialrobot.io/api/mcp
```

Claude (web, desktop) and ChatGPT: add a custom connector with that URL and
sign in. JSON clients (Cursor, VS Code, Windsurf, Zed, OpenClaw):

```json
{"mcpServers":{"socialrobot":{"url":"https://socialrobot.io/api/mcp",
  "headers":{"Authorization":"Bearer ${SOCIALROBOT_API_KEY}"}}}}
```

Drop `headers` to use OAuth instead of a key. Keys:
https://socialrobot.io/scheduler/api-keys. Social accounts are connected by the
user inside SocialRobot (https://socialrobot.io/scheduler/accounts); the agent
never sees network passwords.

No MCP support? Use REST (section 7).

## 2. MCP tools (20)

| Job | Tool |
|---|---|
| Accounts | `list_connected_accounts` |
| Media | `get_media_upload_url` (presigned PUT, preferred), `upload_media` (fallback: public `sourceUrl` or ChatGPT `file`, max 50 MB) |
| Posts | `create_post`, `list_posts`, `update_post`, `reschedule_post`, `delete_post` |
| Network helpers | `tiktok_get_creator_info`, `pinterest_list_boards`, `pinterest_create_board`, `linkedin_search_organizations`, `linkedin_search_people_mentions`, `linkedin_search_geo_locations` |
| Analytics | `get_account_analytics`, `get_post_analytics`, `get_posts_with_analytics`, `get_follower_demographics`, `get_pinterest_top_pins`, `instagram_best_post_times` |

If your client's tool list shows more tools than this, they are newer server
features; read their descriptions and use them.

## 3. Hard rules

1. **Never invent IDs or URLs.** Account IDs come from
   `list_connected_accounts`, board IDs from `pinterest_list_boards`, TikTok
   privacy levels from `tiktok_get_creator_info`, media URLs from the upload
   tools. If a network isn't connected, say so and link the accounts page.
2. **Dates carry an offset.** `scheduledFor.date` and `reschedule_post` take
   ISO 8601 with `Z` or an offset (`2026-11-03T09:00:00-05:00`). Naive times
   are rejected. If the user's timezone is unknown, ask once and reuse it.
3. **Know what each mode does.** `DRAFT` is private. `SCHEDULE` publishes
   automatically at that instant. `NOW` publishes immediately. SocialRobot has
   no second approval step, and a live network post cannot be recalled.
4. **One go-ahead per request, not per post.** If the user already gave the
   copy, the accounts, and the time and said "schedule it" / "post it", that is
   the approval: do it, then report. Read back first only when you filled in
   something yourself (wrote or rewrote copy, picked the time, chose the
   accounts) or the action is `NOW` or a delete. For a batch, show one table and
   get one yes. When the user wants to look first, save `DRAFT`.
5. **Media goes through SocialRobot storage.** Upload, then pass the returned
   `url`. Never pass a local path, Google Drive link, or chat attachment URL
   straight into `create_post`.
6. **Keep the user's words.** Adapt per network only when asked or when a
   limit forces it; show the rewrite instead of silently truncating.
7. **Plan limits are real; don't retry around them.** See section 6.

## 4. Workflow

1. `list_connected_accounts` → pick account IDs.
2. Media (if any): `get_media_upload_url` → HTTP PUT the bytes to
   `presignedUrl` with the same `Content-Type` → keep `url`. If your runtime
   cannot reach the storage host (common in ChatGPT), call `upload_media` with
   a public `sourceUrl` or the chat `file`. Reuse one `url` across targets.
3. Network prep, only for targeted networks:
   - **TikTok**: `tiktok_get_creator_info`. `postMode: "DIRECT_POST"` publishes
     to the profile and needs `privacyLevel` from `privacy_level_options`.
     `postMode: "UPLOAD"` sends a draft to the creator's TikTok inbox to finish
     in the app (no `privacyLevel`). Don't invent posting quotas.
   - **Pinterest**: `pinterest_list_boards` (or `pinterest_create_board`);
     `boardId` is required.
   - **LinkedIn**: `linkedin_search_organizations` (company @mentions, any
     account) and `linkedin_search_people_mentions` (Company Pages only) give
     `type`, `urn`, `displayText` for `captionMentions` with `start`/`length`.
     `linkedin_search_geo_locations` feeds `geoLocations` (Company Page with
     300+ matching followers).
4. `create_post` once per slot, one target object per account, each with its
   own caption. Unused `*Targets` can be omitted or `[]`.
5. Verify: `list_posts` (`status`, `platform`, `dateFrom`/`dateTo`,
   `sort: "scheduled_asc"`, `limit` ≤ 100, `cursor`). Report what is queued,
   where, and when, in the user's timezone.
6. Changes:
   - Time only → `reschedule_post` (`id`, `scheduledFor`).
   - Copy, media, targets, or DRAFT→SCHEDULE → `update_post`. It **replaces**
     the whole post: send every target again and pass existing media URLs to
     keep them.
   - Remove → `delete_post` (drafts and scheduled posts only; name the post when
     confirming).

Example `create_post` (LinkedIn + Instagram, scheduled):

```json
{
  "scheduledFor": {"publish": "SCHEDULE", "date": "2026-11-03T09:00:00-05:00"},
  "linkedinTargets": [{
    "accountId": "<from list_connected_accounts>",
    "caption": "We shipped saved replies today...",
    "medias": {"mediaType": "IMAGE", "medias": [{"mediaUrl": "<url>", "altText": "Saved replies screen"}]}
  }],
  "instagramTargets": [{
    "accountId": "<from list_connected_accounts>",
    "caption": "Saved replies are live ✨ Link in bio.",
    "mediaType": "IMAGE",
    "mediaUrl": "<url>",
    "altText": "Saved replies screen"
  }]
}
```

### Threads on X and Threads

Root post in `caption`/`medias`, follow-ups in an ordered `segments` array on
`twitterTargets[]` or `threadsTargets[]`. Each segment is posted as a reply to
the previous one: up to 24 segments on X (25 posts) and 19 on Threads (20
posts). Per-post limits apply to every segment; `medias` can be `[]`. On Threads, polls,
link attachments, and topic tags belong to the root post only.

### Reporting ("how did last week do?")

`get_posts_with_analytics` (Instagram, Facebook, LinkedIn, Pinterest, Threads,
TikTok; `startDate`/`endDate` as `YYYY-MM-DD`, required for Instagram,
Facebook, LinkedIn, Pinterest). `get_account_analytics` for account trends,
`get_follower_demographics` for audience (Instagram, Facebook, Threads,
LinkedIn Company Pages), `get_pinterest_top_pins` for Pinterest. Lead with the
top 3 posts and one thing to repeat; don't invent benchmarks. TikTok returns
public videos only and needs the `video.list` permission (reconnect if missing).

## 5. Network rules

| Network | Caption | Media | Notes |
|---|---|---|---|
| Instagram | 2,200 | Required. 1 image/video, carousel 2–10, Reels, Stories | Business/Creator account. `firstComment`, `collaborators`, `userTags`, `altText` |
| Facebook | 63,206 | 0–10, images **or** videos, never mixed | Pages. `isReel`, `isStory`, slideshow (3–7 images) |
| LinkedIn | 3,000 | 1–20 images, 1 video, or 1 document | Profile or Company Page. `firstComment`, `poll`, mentions |
| X (Twitter) | 280 | 0–4 (IMAGE, VIDEO, GIF) | **Pro plan only.** Threads via `segments` |
| TikTok | 2,200 | 1 video, or 1–35 photos | Creator info first; `DIRECT_POST` or `UPLOAD`; title ≤ 90 on photo posts |
| Threads | 500 | 0–20 | Polls (2–4 options) and link attachments text-only; `topicTag` ≤ 50 |
| Pinterest | title 100, description 800 | 1–5 images or 1 video | `boardId` required |
| Bluesky | 300 | 1–4 images or 1 video in `embed` | Images under 2 MB |
| Mastodon | 500 (instance can differ) | 0–4 | `visibility`, content warnings, polls (no media with polls) |

Sizes and safe margins: social-media-image-sizes. Full payload shapes:
`references/rest-api.md` and https://socialrobot.io/api/llm.txt.

## 6. Plans (live values: https://socialrobot.io/pricing.md)

| Plan | Price | Accounts | Scheduled posts / month | X |
|---|---|---|---|---|
| Free | $0 | 2 | 15 | No |
| Starter | $19/mo or $190/yr | 5 | Unlimited | No |
| Pro | $29/mo or $290/yr | 20 | Unlimited | Yes |
| Enterprise | Custom | Unlimited | Unlimited | Yes |

REST API and MCP are on every plan. If a call fails on a limit (X below Pro,
over 15 scheduled posts on Free, too many accounts), say which limit and link
https://socialrobot.io/pricing. Don't retry or split the post to dodge it.
Rate limit: 2,000 requests/minute and 200,000/day per account, shared by REST
and MCP.

## 7. REST (no MCP)

Base `https://socialrobot.io/api/v1`, header `x-api-key: $SOCIALROBOT_API_KEY`.
Same rules as above. Endpoints, curl, and payloads: `references/rest-api.md`.
Analytics beyond TikTok, best times, and LinkedIn search are MCP-only.

## 8. Errors

- 401 / missing scope: key missing or wrong, or OAuth scope not granted; the
  user reconnects and approves it.
- 403: account ownership problem (the ID isn't one of this user's accounts).
- Post `FAILED` or `PARTIALLY_PUBLISHED`: `list_posts` with that `status`,
  report the per-network error. Usually an expired network login; the user
  reconnects that account in SocialRobot.
- Validation errors (Instagram without media, Pinterest without board,
  Facebook mixed media, Threads poll with media, naive date): fix the input,
  don't retry blindly.

More: https://socialrobot.io/llms.txt · https://socialrobot.io/agent-instructions.md
