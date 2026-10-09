# SocialRobot REST API quick reference

Source of truth: https://socialrobot.io/api/openapi.json (OpenAPI 3) and
https://socialrobot.io/api/llm.txt. Checked 2026-10-09.

- Base URL: `https://socialrobot.io/api/v1` (legacy unversioned `/api/...`
  paths still work).
- Auth: `x-api-key: sk_...` (keys at https://socialrobot.io/scheduler/api-keys).
- Rate limit: 2,000 requests/min and 200,000/day per account, shared with MCP.
- Errors: `{"code": "...", "message": "...", "issues": []}`; 400 invalid,
  401 bad/missing key, 403 account ownership, 404 account/board not found.

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| GET | `/accounts` | Connected accounts (`id`, `platform`, `handle`, `expiresAt`) |
| POST | `/media/upload-url` | `{filename, contentType}` → `presignedUrl`, `url`; PUT bytes to `presignedUrl`, use `url` |
| POST | `/media/from-url` | `{sourceUrl, filename, contentType}` → `url` (max 50 MB). Fallback when PUT isn't possible |
| POST | `/posts` | One post, many accounts. Send all nine `*Targets` arrays (`[]` for unused) |
| GET | `/posts` | List; query `status`, `platform`, `limit` (1–100), `cursor` |
| GET | `/posts/{id}` | One post |
| PATCH | `/posts/{id}` | Replace a draft or scheduled post (full payload, same shape as `POST /posts`) |
| PATCH | `/posts/{id}/schedule` | `{"scheduledFor": "<ISO with offset>"}` |
| DELETE | `/posts/{id}` | Delete a draft or scheduled post |
| POST | `/{instagram,facebook,linkedin,x,tiktok,threads,pinterest,bluesky,mastodon}/create` | Single-network create: `{scheduledFor, post: {...}}` |
| GET | `/tiktok/creator-info/{accountId}` | Privacy options, max duration (call before TikTok `DIRECT_POST`) |
| GET | `/tiktok/posts-with-analytics/{accountId}` | Public TikTok videos with metrics (`limit`, `startDate`, `endDate`) |
| GET / POST | `/pinterest/boards/{accountId}` | List / create boards (`{name, description, privacy}`) |
| GET | `/pinterest/boards/{accountId}/{boardId}` | One board |

`status` values: `DRAFT`, `SCHEDULED`, `PUBLISHING`, `PUBLISHED`,
`PARTIALLY_PUBLISHED`, `FAILED`.

`scheduledFor`: `{"publish":"DRAFT"}`, `{"publish":"NOW"}`, or
`{"publish":"SCHEDULE","date":"2026-11-03T09:00:00-05:00"}` (offset or `Z`
required).

## curl

```bash
SR=https://socialrobot.io/api/v1; H="x-api-key: $SOCIALROBOT_API_KEY"

curl -s "$SR/accounts" -H "$H"

# media: presign, upload, keep .url
curl -s -X POST "$SR/media/upload-url" -H "$H" -H "Content-Type: application/json" \
  -d '{"filename":"launch.jpg","contentType":"image/jpeg"}' > up.json
curl -s -X PUT "$(jq -r .presignedUrl up.json)" -H "Content-Type: image/jpeg" --data-binary @launch.jpg
MEDIA_URL=$(jq -r .url up.json)

# schedule on LinkedIn + Bluesky (all nine target arrays present)
curl -s -X POST "$SR/posts" -H "$H" -H "Content-Type: application/json" -d @- <<JSON
{"scheduledFor":{"publish":"SCHEDULE","date":"2026-11-03T09:00:00-05:00"},
 "linkedinTargets":[{"accountId":"<id>","caption":"Saved replies are live.",
   "medias":{"mediaType":"IMAGE","medias":[{"mediaUrl":"$MEDIA_URL","altText":"Saved replies"}]}}],
 "blueskyTargets":[{"accountId":"<id>","caption":"Saved replies are live 🦋",
   "embed":{"mediaType":"IMAGE","medias":[{"url":"$MEDIA_URL","alt":"Saved replies"}]}}],
 "instagramTargets":[],"pinterestTargets":[],"twitterTargets":[],"tiktokTargets":[],
 "mastodonTargets":[],"threadsTargets":[],"facebookTargets":[]}
JSON

curl -s "$SR/posts?status=SCHEDULED&limit=20" -H "$H"
curl -s -X PATCH "$SR/posts/<id>/schedule" -H "$H" -H "Content-Type: application/json" \
  -d '{"scheduledFor":"2026-11-04T10:00:00-05:00"}'
curl -s -X DELETE "$SR/posts/<id>" -H "$H"   # confirm with the user first
```

## Target field cheat sheet (unified `POST /posts`, also MCP `create_post`)

Target objects are flat (no nested `post` key).

- `instagramTargets[]`: `accountId`, `caption`, `mediaType` (`IMAGE`/`VIDEO`/`CAROUSEL`),
  `mediaUrl` or `medias[]` (carousel 2–10; carousel requires `isStory` and `isReel`), `isStory`, `isReel`, `coverUrl`,
  `altText`, `firstComment`, `collaborators`, `userTags`, `location`, `hashtags`.
- `facebookTargets[]`: `accountId`, `caption`, `medias[]` (`mediaType`, `mediaUrl`, `altText`),
  `isReel`, `isStory`, `isSlideshow`, `slideshowDuration`, `slideshowTransition`, `coverUrl`.
- `linkedinTargets[]`: `accountId`, `caption`, `visibility` (`PUBLIC`/`CONNECTIONS`),
  `medias: {mediaType: IMAGE|VIDEO|DOCUMENT, medias: [...]}` (video needs `videoMetadata`;
  document uses `documentTitle`), `firstComment`, `poll`, `article`,
  `captionMentions`, `firstCommentMentions`, `geoLocations`.
- `twitterTargets[]`: `accountId`, `caption`, `medias[]` (0–4; `IMAGE`/`VIDEO`/`GIF`, `altText`),
  `segments[]` (≤ 24, each `{caption, medias}`).
- `threadsTargets[]`: `accountId`, `caption`, `medias[]` (≤ 20), `linkAttachment`, `poll`,
  `topicTag`, `userTags`, `hashtags`, `segments[]` (≤ 19).
- `tiktokTargets[]`: `accountId`, `caption`, `postMode` (`DIRECT_POST`/`UPLOAD`), `privacyLevel`
  (DIRECT_POST only), `medias: {mediaType: VIDEO|IMAGE, medias: [...]}`, `title`,
  `photoCoverIndex`, `disableComment`, `disableDuet`, `disableStitch`, `autoAddMusic`,
  `commercialContent {isCommercialContent, yourBrand, brandedContent}`, `isAigc`.
- `pinterestTargets[]`: `accountId`, `boardId`, `title`, `description`, `link`, `altText`,
  `boardSectionId`, `mediaType`, `medias: {mediaType, medias: [...]}`.
- `blueskyTargets[]`: `accountId`, `caption`, `embed: {mediaType, medias: [{url, alt}]}`.
- `mastodonTargets[]`: `accountId`, `caption`, `visibility`, `sensitive`, `spoilerText`,
  `language`, `poll`, `medias[]` (`name`, `mediaUrl`, `size`, `mediaType`, `alt`).
