# Best time to post: SocialRobot tables for 9 networks

Source: the static per-network tables SocialRobot's composer uses, published at
https://socialrobot.io/best-time-to-post/{instagram,tiktok,linkedin,facebook,x,pinterest,threads,bluesky,mastodon}
(Spanish: https://socialrobot.io/es/best-time-to-post). Composer defaults last
updated Aug 15, 2026; checked 2026-10-09.

These are product defaults, not a forecast for one audience. Read each time as
the **audience's local clock**. The data leans toward US audiences; for
followers elsewhere, keep the same local-hour pattern in their timezone.
"—" = no strong window (not "never post").

| Network | Mon | Tue | Wed | Thu | Fri | Sat | Sun |
|---|---|---|---|---|---|---|---|
| Instagram | 3–6 PM | 6–9 AM, 12–2 PM, 3–6 PM | 6–9 AM, 12–2 PM, 3–6 PM | 3–6 PM | 6–9 AM, 12–2 PM, 3 PM, 5–6 PM | — | — |
| TikTok | 4–9 PM | 12–3 PM, 4–9 PM | 5–9 PM | 4–9 PM | 12–3 PM, 4–9 PM | — | 8 PM |
| LinkedIn | 7 AM–5 PM | 7 AM–5 PM (peak ~9 AM) | 7 AM–5 PM (peak ~9 AM) | 7 AM–5 PM (peak ~9 AM) | 7 AM–5 PM | — | — |
| Facebook | 9 AM–12 PM | 9 AM–12 PM | 8–11 AM, 3–5 PM | 8 AM–12 PM | 9–10 AM | 9–10 AM | 8 AM–12 PM |
| X (Twitter) | 8 AM–3 PM | 8 AM–3 PM (peak ~8 AM) | 8 AM–3 PM (peak ~9 AM) | 8 AM–3 PM | 8 AM–3 PM | — | — |
| Pinterest | 2–4 PM | 2–4 PM | 2–4 PM | 2–4 PM | 1–3 PM (peak) | — | — |
| Threads | 7, 9 AM | 7, 9 AM, 3 PM | 7, 9 AM | 7, 9 AM, 11 PM | 7, 9 AM | 7, 9 AM, 12 PM | 7, 9 AM, 1 PM (peak) |
| Bluesky | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | 8 AM–1 PM | — | — |
| Mastodon | 8 AM–3 PM | 8 AM–3 PM (peak ~8 AM) | 8 AM–3 PM (peak ~9 AM) | 8 AM–3 PM | 8 AM–3 PM | — | — |

## Quick answers

- **Best time to post on Instagram**: Tuesday or Wednesday, 6–9 AM, 12–2 PM,
  or 3–6 PM. Once a week → Tue or Wed morning. Three a week → Tue, Wed, Fri.
- **TikTok**: afternoons and evenings; Tue and Fri add 12–3 PM; Sunday peaks
  at 8 PM; Saturday empty.
- **LinkedIn**: weekdays 7 AM–5 PM; Tue–Thu ~9 AM is the first slot to fill.
- **Facebook**: mid-morning; Wednesday adds 3–5 PM; Fri/Sat only 9–10 AM.
- **X**: weekdays 8 AM–3 PM; Tue ~8 AM, Wed ~9 AM.
- **Pinterest**: weekday afternoons (2–4 PM; Fri 1–3 PM).
- **Threads**: early mornings every day (7 and 9 AM).
- **Bluesky**: weekdays 8 AM–1 PM. **Mastodon**: weekdays 8 AM–3 PM; replies
  and hashtags matter more than the clock.

## Cross-posting one post to several networks

- LinkedIn + X + Bluesky + Mastodon overlap weekdays 8 AM–1 PM; 9 AM Tue–Thu
  is the safe default.
- Instagram + Facebook overlap is thin: Wed 8–9 AM fits both; Tue and Fri 9 AM
  sit on the edge. If there's no overlap, use the primary network's slot or
  schedule separate posts.
- Instagram + TikTok: Tue or Fri 12–2 PM, or Mon/Thu 4–6 PM.

## Personalized Instagram times

MCP `instagram_best_post_times` (`accountId` of a connected Instagram account,
scope `analytics:read`) returns guidance from that account's own past posts.
Prefer it over the table for that account and say where it came from. Too
little data → fall back to the table and say why. Only Instagram has this
today. For a rough read on other networks, group `get_posts_with_analytics`
results by weekday and hour and label it a small sample.
