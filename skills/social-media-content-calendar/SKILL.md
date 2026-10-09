---
name: social-media-content-calendar
description: >-
  Build a social media content calendar for a week, a month, or a launch, pick
  the best time to post on each network (personalized Instagram best times plus
  SocialRobot's best-time tables for Instagram, TikTok, LinkedIn, Facebook, X,
  Pinterest, Threads, Bluesky, Mastodon), write platform-native drafts, and,
  once the user approves the plan, schedule every post through the SocialRobot
  MCP server (https://socialrobot.io/api/mcp) or REST API. Use for "plan next
  week's posts", "content calendar for November", "launch week campaign",
  "what's the best time to post on Instagram / LinkedIn / TikTok", "when should
  I post this", "turn this blog post into a week of posts", "fill the empty days
  on my calendar". Pairs with strategy skills like social-content (pillars and
  hooks) and with social-media-manager (single posts, edits, analytics).
homepage: https://socialrobot.io/best-time-to-post
metadata: {"openclaw":{"emoji":"🗓️","homepage":"https://socialrobot.io/best-time-to-post","primaryEnv":"SOCIALROBOT_API_KEY","requires":{"anyBins":["curl"]},"envVars":[{"name":"SOCIALROBOT_API_KEY","required":false,"description":"SocialRobot API key (sk_...). Only needed to save or schedule the calendar over REST or a key-based MCP client. Planning and best-time answers work without any account; not needed when the MCP server is connected with OAuth."}]}}
---

# Social media content calendar (SocialRobot)

Goal: turn a goal, a date range, or a source asset into a reviewed set of
platform-native posts sitting on the SocialRobot calendar at good times.

Flow: **plan table → user approves → schedule**. Planning needs no account.
Saving and scheduling need the SocialRobot MCP server
(`https://socialrobot.io/api/mcp`; setup in social-media-manager or
https://socialrobot.io/mcp) or the REST API.

## Hard rules

1. **Plan first, schedule after one approval.** Show the full calendar table
   (date, time with timezone, network, copy, media). One "yes" covers the
   batch; don't ask post by post. Then schedule it all.
2. **Never `NOW` from a calendar.** Use `SCHEDULE` with a future date, or
   `DRAFT` if the user wants to edit inside SocialRobot first.
3. **Dates carry an offset** (`2026-11-03T09:00:00-05:00`). Confirm the
   audience timezone once if unknown.
4. **No invented facts.** No made-up stats, prices, testimonials, or account
   IDs. Use the user's offer and the source material.
5. **Check the plan's room before scheduling.** Free = 15 scheduled posts per
   month and 2 accounts; X needs Pro (https://socialrobot.io/pricing.md). If
   the batch won't fit, say so before creating anything, not halfway through.
6. **Times are guidance.** If the user names a time, keep it. Mention a
   stronger window once, only if theirs is far off.

## Workflow

1. **Scope.** Ask only for what's missing: date range, audience timezone,
   networks, posts per week, goal (steady presence, launch, offer, event), fixed
   dates, and any assets.
2. **What's already there** (if connected): `list_connected_accounts`, then
   `list_posts` with `dateFrom`/`dateTo` (ISO), `sort: "scheduled_asc"`. Fill
   gaps; don't double-book a network on one day.
3. **Pick slots.**
   - Instagram connected: `instagram_best_post_times` with its `accountId` for
     personalized windows; prefer them and say so.
   - Everything else: the SocialRobot best-time table below (full notes in
     `references/best-times.md`).
   - Spread posts over the strong days instead of stacking one day. Use the
     start of a window, rounded to :00 or :30.
4. **Mix.** A useful week: teach, proof (customer/result), behind the scenes,
   opinion, question, offer. Shift toward offers only in launch or sale weeks.
   If a strategy skill (e.g. `social-content`) already produced pillars or
   hooks, use those.
5. **Draft per network, not copy-paste.** LinkedIn: hook in the first ~210
   characters, up to 3,000. X: one idea in 280, or a thread (`segments`).
   Threads: conversational, 500. Bluesky: 300. Instagram and TikTok need media.
   Pinterest needs a board and a 2:3 image. Mastodon: 500, light on hashtags.
6. **Show the calendar table** and wait for approval:

   | # | When (audience tz) | Network(s) | Angle | Copy | Media |
   |---|---|---|---|---|---|
   | 1 | Tue Nov 3, 9:00 AM ET | LinkedIn, X | Teach | … | carousel.pdf |

7. **Media.** Size with social-media-image-sizes. Upload each asset once
   (`get_media_upload_url` + PUT, or `upload_media` with a public `sourceUrl`)
   and reuse the returned `url` across targets.
8. **Schedule.** One `create_post` per slot with every target account for that
   slot, `scheduledFor: {"publish": "SCHEDULE", "date": "<ISO with offset>"}`.
   Pinterest needs `boardId` (`pinterest_list_boards`); TikTok needs
   `tiktok_get_creator_info` first (`DIRECT_POST` + `privacyLevel`, or
   `UPLOAD` to the TikTok inbox).
   If the user chose to review in SocialRobot first, use `"DRAFT"`; later
   convert with `update_post` (full post, same media URLs, `SCHEDULE` + date).
9. **Verify and report.** `list_posts` for the range; give count per day and
   network and the calendar link https://socialrobot.io/scheduler. Scheduled
   posts publish automatically at their time.

## Best time to post (SocialRobot composer tables)

Audience-local time. "—" = no strong window.

| Network | Mon | Tue | Wed | Thu | Fri | Sat | Sun |
|---|---|---|---|---|---|---|---|
| Instagram | 3–6 PM | 6–9 AM, 12–2 PM, 3–6 PM | 6–9 AM, 12–2 PM, 3–6 PM | 3–6 PM | 6–9 AM, 12–2 PM, 3 PM, 5–6 PM | — | — |
| TikTok | 4–9 PM | 12–3 PM, 4–9 PM | 5–9 PM | 4–9 PM | 12–3 PM, 4–9 PM | — | 8 PM |
| LinkedIn | 7 AM–5 PM | 7 AM–5 PM (~9 AM) | 7 AM–5 PM (~9 AM) | 7 AM–5 PM (~9 AM) | 7 AM–5 PM | — | — |
| Facebook | 9 AM–12 PM | 9 AM–12 PM | 8–11 AM, 3–5 PM | 8 AM–12 PM | 9–10 AM | 9–10 AM | 8 AM–12 PM |
| X (Twitter) | 8 AM–3 PM | 8 AM–3 PM (~8 AM) | 8 AM–3 PM (~9 AM) | 8 AM–3 PM | 8 AM–3 PM | — | — |
| Pinterest | 2–4 PM | 2–4 PM | 2–4 PM | 2–4 PM | 1–3 PM | — | — |
| Threads | 7, 9 AM | 7, 9 AM, 3 PM | 7, 9 AM | 7, 9 AM, 11 PM | 7, 9 AM | 7, 9 AM, 12 PM | 7, 9 AM, 1 PM |
| Bluesky | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | — | — |
| Mastodon | 8 AM–3 PM | 8 AM–3 PM (~8 AM) | 8 AM–3 PM (~9 AM) | 8 AM–3 PM | 8 AM–3 PM | — | — |

Answering a pure timing question ("best time to post on Instagram?"): give the
strongest day(s) and window, one line on the pattern, the timezone caveat, and
offer to schedule. Consistency beats the perfect minute; say so if the user is
over-tuning. Spanish: answer with 24-hour times and link
https://socialrobot.io/es/best-time-to-post.

## Repurposing one asset into a week

Read the source (article, transcript, release notes) and pull 3–6 distinct
points. Map: X thread (one point per post), LinkedIn long post (the story),
Instagram carousel (the steps), Threads/Bluesky short takes spread over days,
Pinterest pin (the how-to, linking back). Keep the source's facts.

## Canva

In the SocialRobot app, Canva designs can be attached to a draft from the media
picker (https://socialrobot.io/features/canva). Free Canva is enough unless a
design uses Pro elements the user hasn't paid for; then Canva blocks the export. From an agent with a Canva connector:
export the designs, `upload_media` with the export URL, then follow the flow
above.

Not for: one-off posts or edits (social-media-manager), image dimensions
(social-media-image-sizes).
