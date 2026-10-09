# SocialRobot social media skills

Agent skills that plan, size, and **actually publish** social media posts on
Instagram, Facebook, LinkedIn, X (Twitter), TikTok, Threads, Pinterest,
Bluesky, and Mastodon, through the [SocialRobot](https://socialrobot.io) MCP
server (`https://socialrobot.io/api/mcp`) or REST API. Works in Claude, Claude
Code, ChatGPT, Cursor, Codex, Gemini CLI, OpenClaw, and any client that loads
`SKILL.md` skills.

| Skill | What it does |
|---|---|
| [`social-media-manager`](skills/social-media-manager/SKILL.md) | The doer: schedule, publish, edit, reschedule, delete, and report on posts across 9 networks via 20 MCP tools or REST. |
| [`social-media-content-calendar`](skills/social-media-content-calendar/SKILL.md) | Plan a week/month/launch, pick the best time to post per network (personalized Instagram times + tables for all 9), show drafts, schedule after one approval. |
| [`social-media-image-sizes`](skills/social-media-image-sizes/SKILL.md) | 2026 image and video sizes, safe margins, file caps, and character limits for the 9 networks; resize, then upload via SocialRobot media tools. |

## Install

```bash
# all three
npx skills add socialrobot-io/social-media-skills

# or one at a time
npx skills add socialrobot-io/social-media-skills --skill social-media-manager
npx skills add socialrobot-io/social-media-skills --skill social-media-content-calendar
npx skills add socialrobot-io/social-media-skills --skill social-media-image-sizes
```

Manual install: copy a skill folder into your agent's skills directory (for example `.claude/skills/`).

## Connect SocialRobot (needed for publishing)

```bash
claude mcp add --transport http socialrobot https://socialrobot.io/api/mcp
```

Or add `{"mcpServers":{"socialrobot":{"url":"https://socialrobot.io/api/mcp"}}}`
to your client and sign in. For key-based clients, create a key at
https://socialrobot.io/scheduler/api-keys and send
`Authorization: Bearer sk_...` (REST uses `x-api-key`). Connect your social
accounts inside SocialRobot first.

Sizes and best-time answers work without an account.

## Pairs well with

Strategy skills decide *what* to post; these skills get it on the calendar.

```bash
npx skills add coreyhaines31/marketingskills --skill social-content
```

## Plans

Free: 2 accounts, 15 scheduled posts/month. Starter $19/mo: 5 accounts,
unlimited posts. Pro $29/mo: 20 accounts, adds X (Twitter). MCP and REST on
every plan. Current limits: https://socialrobot.io/pricing.md

## Docs

- MCP: https://socialrobot.io/mcp
- REST + MCP reference for agents: https://socialrobot.io/api/llm.txt
- OpenAPI: https://socialrobot.io/api/openapi.json
- Best times to post: https://socialrobot.io/best-time-to-post
- Image sizes: https://socialrobot.io/sizes

## License

MIT
