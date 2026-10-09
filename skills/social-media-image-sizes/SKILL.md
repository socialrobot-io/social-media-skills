---
name: social-media-image-sizes
description: >-
  Social media image sizes, video specs, safe margins, and character limits for
  2026 on Instagram (feed 4:5 1080x1350, Stories and Reels 9:16), TikTok,
  Facebook, LinkedIn, X (Twitter), Threads, Pinterest (1000x1500 pins), Bluesky,
  and Mastodon, matching what SocialRobot prepares at publish. Resize or crop
  one master image for cross-posting, check file-size caps, then upload it with
  the SocialRobot MCP media tools so it publishes without bad crops. Use for
  "Instagram post size", "social media image sizes cheat sheet", "Pinterest pin
  size", "Facebook story size", "LinkedIn image size", "one image for Instagram,
  Facebook and LinkedIn", "my image got cropped", "character limit for LinkedIn
  / X / Threads". Scheduling the post is social-media-manager.
homepage: https://socialrobot.io/sizes
metadata: {"openclaw":{"emoji":"📐","homepage":"https://socialrobot.io/sizes","requires":{"anyBins":["magick","convert","ffmpeg","python3"]},"envVars":[{"name":"SOCIALROBOT_API_KEY","required":false,"description":"Only needed to upload the finished file to SocialRobot over REST or a key-based MCP client. Size answers need no account."}]}}
---

# Social media image sizes (SocialRobot publish specs)

Source: https://socialrobot.io/sizes and the per-network pages
(`/sizes/instagram`, `/sizes/tiktok`, `/sizes/facebook`, `/sizes/x`,
`/sizes/linkedin`, `/sizes/pinterest`, `/sizes/threads`, `/sizes/bluesky`,
`/sizes/mastodon`), aligned with what SocialRobot prepares at publish.
Checked 2026-10-09. Networks change limits; when a number matters, link the
page.

## Hard rules

1. Answer from the tables below; don't invent dimensions for placements that
   aren't listed (say "not listed" and give the closest safe choice).
2. Never letterbox: crop to the target aspect, keep the subject and text
   inside the safe area.
3. Check file size, not just pixels. Bluesky (2 MB) and X/LinkedIn (5 MB) are
   where uploads fail.
4. Show the user the crop before uploading when a center crop could cut faces
   or text.
5. Upload the finished file through SocialRobot (below); never hand a local
   path or Drive link to `create_post`.

## Sizes by network

| Network | Placement | Size | Aspect | Max file | Formats / notes |
|---|---|---|---|---|---|
| Instagram | Feed / carousel image | **1080×1350** | 4:5 (legal 4:5–1.91:1) | 8 MB | JPEG; PNG converted at publish |
| Instagram | Story image | 1080×1920 | 9:16 | 8 MB | JPEG |
| Instagram | Reel / feed video | 1080×1920 | 9:16 | 100 MB | MP4, MOV. Reels ≤ 90 s, feed ≤ 60 s, Stories ≤ 15 s |
| TikTok | Photo / carousel | 1080×1920 | 9:16 | 20 MB each | JPEG, WebP (PNG converted). Up to 35 images |
| TikTok | Video | 1080×1920 | 9:16 | 4 GB | MP4, WebM, MOV. 3–600 s |
| Facebook | Feed image | Long edge ≤ 2048 | Flexible | 10 MB | JPEG, PNG, GIF, BMP, TIFF |
| Facebook | Story image | 1080×1920 | 9:16 | 10 MB | |
| Facebook | Reel / Story video | 1080×1920 | 9:16 | 2 GB | MP4 (Reels also MOV) |
| Facebook | Link preview (OG) | 1200×630 | 1.91:1 | ~8 MB | JPEG, PNG |
| Facebook | Page cover | 820×312 | ~2.63:1 | keep small | JPEG, PNG |
| X | Post image | Long edge ≤ 2048 | Flexible (16:9 or 1:1 crops) | 5 MB | JPEG, PNG, GIF, WebP. ≤ 4 media |
| X | GIF | ≤ 1280×1080 | Flexible | 15 MB | |
| X | Video | ≤ 1280×1024 | 1:3–3:1 | 8 GB | MP4 (H.264), MOV. 0.5 s–20 min (125 min Premium) |
| LinkedIn | Post image | Long edge ≤ 2048 | Flexible | 5 MB | JPEG, PNG, GIF. ≤ 20 images |
| LinkedIn | Video | Flexible | Flexible | 200 MB | MP4. 3–600 s |
| LinkedIn | Document (carousel) | — | — | 100 MB | PDF, PPT(X), DOC(X). ≤ 300 pages |
| Pinterest | Image pin | **1000×1500** | 2:3 | 20 MB | JPEG, PNG, WebP, BMP, TIFF. ≤ 5 images |
| Pinterest | Video pin | Flexible | Flexible | 2 GB | MP4, MOV, M4V. 4–900 s |
| Threads | Post image | Long edge ≤ 1440 | Flexible (4:5–1.91:1 recommended) | 8 MB | JPEG, PNG. Carousel 2–20 |
| Threads | Story image | 1080×1920 | 9:16 | 8 MB | |
| Threads | Video | Flexible | Flexible | 1 GB | MP4, MOV. 3–300 s |
| Bluesky | Post image | Long edge ≤ 2048 | Flexible | **2 MB** | JPEG, PNG, GIF. ≤ 4 images |
| Bluesky | Video | Flexible | Flexible | 100 MB | MP4 only |
| Bluesky | Banner / avatar | 1500×500 / 400×400 | 3:1 / 1:1 | ~1 MB / 2 MB | |
| Mastodon | Post image | Long edge ≤ 3840, ≤ 8.3 MP | Flexible | 16 MB (default) | JPEG, PNG, GIF, WebP. ≤ 4 attachments |
| Mastodon | Video | Long edge ≤ 1920 | Flexible | 40 MB (default) | MP4, WebM, MOV. ≤ 300 s. Instances can differ |

What SocialRobot does at publish: Instagram stills become JPEG and are fitted
into 4:5–1.91:1; TikTok photos are cropped to 9:16 (PNG → JPEG); Pinterest tall
images default to a 2:3 crop; X, LinkedIn, Threads, Bluesky, Mastodon keep the
source aspect and scale down unless the user crops. MOV/WebM become MP4 in the
composer for LinkedIn and Bluesky.

## One master for cross-posting

- Instagram + Facebook + Threads + LinkedIn (+ X, Bluesky): one **1080×1350
  (4:5)** image. Compress a copy under 2 MB for Bluesky.
- Stories, Reels, TikTok photos: a separate **1080×1920 (9:16)**. Don't
  letterbox a 4:5 card.
- Pinterest as a main channel: its own **1000×1500 (2:3)**. Never send a 2:3
  master to Instagram (outside the legal range, it gets cropped).
- Link-share image: 1200×630.

## Safe margins

Keep logos, headlines, handles, and CTAs about 6% in from every edge (≈64 px on
a 1080-wide canvas). On 9:16, keep text out of the top ~250 px and bottom
~265 px (profile row, caption, and buttons cover them) and away from the right
edge on TikTok (action buttons). Banners (Bluesky 1500×500, Facebook 820×312):
logo and text in the center third. When generating with an image model, ask for
the final aspect (4:5, 9:16, 2:3) and put the margin rule in the prompt instead
of cropping afterwards.

## Resize and compress locally

```bash
magick in.jpg -resize 1080x1350^ -gravity center -extent 1080x1350 -quality 88 feed-4x5.jpg
magick in.jpg -resize 1080x1920^ -gravity center -extent 1080x1920 -quality 88 story-9x16.jpg
magick in.jpg -resize 1000x1500^ -gravity center -extent 1000x1500 -quality 88 pin-2x3.jpg
magick feed-4x5.jpg -strip -quality 80 bluesky.jpg          # aim < 2 MB
ffmpeg -i in.mov -vf "scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920" \
  -c:v libx264 -crf 23 -c:a aac reel-9x16.mp4
```

No local tools? Free browser tools: https://socialrobot.io/tools (Instagram,
TikTok, LinkedIn, Pinterest, Open Graph, Facebook cover resizers; image
compressor; Instagram carousel slicer).

## Character limits (what SocialRobot validates)

| Network | Field | Limit | Feed fold (approx.) |
|---|---|---|---|
| X | Post | 280 | — |
| Instagram | Caption / bio | 2,200 / 150 | ~125 |
| LinkedIn | Post / headline / comment | 3,000 / 220 / 1,250 | ~210 |
| Threads | Post | 500 | — |
| Bluesky | Post | 300 | — |
| TikTok | Caption / bio | 2,200 / 80 | ~100 |
| Facebook | Post | 63,206 | ~477 |
| Mastodon | Post | 500 (instance default) | — |
| Pinterest | Title / description | 100 / 800 via API (Pinterest shows ~500; keep it under) | ~50 |

Source: https://socialrobot.io/tools/social-media-character-limits. Threads and
Bluesky count graphemes; X uses Twitter weighting (links and some emoji count
extra).

## Upload to SocialRobot (MCP)

1. `get_media_upload_url` with `filename` and `contentType` → HTTP PUT the file
   bytes to `presignedUrl` with that `Content-Type` → keep `url`.
2. Runtime can't reach the storage host (common in ChatGPT)? `upload_media`
   with a public https `sourceUrl` (or the chat `file`); max 50 MB.
3. Pass `url` into `create_post` (see social-media-manager), or hand it to
   social-media-content-calendar for a batch.

REST equivalent: `POST https://socialrobot.io/api/v1/media/upload-url` then
PUT, or `POST /media/from-url` (header `x-api-key`).
