# TourMind Booking MCP Product Package

This folder contains the product definition and the user-installable companion Skill for TourMind Booking MCP. It intentionally does not contain MCP server source code, deployment scripts, or runtime-specific implementation.

## Package contents

```text
Tourmind-Booking-Mcp/
├── README.md
├── TourMind MCP-FORMAT.md
└── skill/
    └── tourmind-booking/
        ├── SKILL.md
        └── references/
            └── parameter_guide.md
```

- `skill/tourmind-booking/` is distributed to users and defines the Agent's hotel-search, price-query, booking, payment, and update behavior.
- `TourMind MCP-FORMAT.md` defines the user-facing MCP connection example and the capability contract for the development team.

## Product model

The MCP service provides stable enterprise hotel tools, including `batch_query_room_rates` for partial-success live-rate retrieval across up to 20 hotels. Search, single/batch rate, availability, and booking tools support repeated per-room adult/child occupancy. The local Skill defines the current user workflow and can evolve independently as search, ranking, price verification, booking, and display strategies improve.

Hotel-list and hotel-detail tools may return a read-only `data.web_url`. The companion Skill localizes the returned page to Chinese, English, Japanese, Korean, Spanish, or Arabic according to the user's language while preserving the complete access ticket; unsupported languages fall back to English.

## Skill version and updates

The installed `SKILL.md` frontmatter is the single source of truth for the Skill version:

```yaml
metadata:
  author: TourMind
  version: "<current-version>"
```

The Agent sends `metadata.version` only to `check_skill_update` when an update check is due. Hotel and order tools never receive a Skill version, and users never maintain it in the MCP connection configuration.

## Authentication setup

Sign in to your TourMind account, then visit [Create a Skill Token](https://tourmind.com/user/skill-token) to create the token used by the MCP connection.

If you do not have an account, register for a business account at [Business account registration](https://tourmind.com/admin/skillSignup). Developers and individual users should use the TourMind Skill version intended for their user type instead.

Configure the token as the MCP connection's Bearer credential and reconnect. Never paste the token into a conversation or expose it in prompts, logs, screenshots, commits, or issue reports.

## User update experience

The Agent calls `check_skill_update` once when the Skill is first used in every new conversation, and again when an existing conversation resumes after at least 24 hours of inactivity. When that tool returns a visible `skill_update`:

1. Complete the user's current hotel request first.
2. Show the release message and explain that updating is recommended for TourMind's latest hotel-search and price-query strategy because older endpoints may become unavailable.
3. Offer to update from the official sources listed by `release_source_url`.
4. Obtain confirmation before changing local Skill files.
5. Update the Skill and frontmatter `metadata.version` together.
6. Validate that the installed `metadata.version` matches `latest_version`.
7. Use the new version in the next scheduled update check without asking the user to edit the MCP connection.

## Development handoff

The server implementation must satisfy `TourMind MCP-FORMAT.md`, including all eleven tools referenced by the companion Skill. Transport framework, hosting, deployment, internal forwarding, and release infrastructure are development decisions and are outside this package.

## TourMind hotel booking ecosystem

Choose the package that matches the audience and connection model:

| Audience | Integration | Authentication model | Repository |
|---|---|---|---|
| Consumer / ToC | Direct HTTP Skill | Public search and availability; `user_key` only for order operations | [Hotel Booking AI](https://github.com/tourmind-com/Hotel-Booking-AI) |
| Business / ToB | Direct HTTP Skill | Skill Token required for every API call | [TourMind Booking Skill](https://github.com/tourmind-com/Tourmind-Booking-Skills) |
| Consumer / ToC | MCP package + companion Skill | Public MCP connection; `user_key` only for order operations | [Hotel Booking AI MCP](https://github.com/tourmind-com/Hotel-Booking-AI-MCP) |
| Business / ToB | MCP package + companion Skill | Bearer-authenticated MCP connection | **[TourMind Booking MCP](https://github.com/tourmind-com/Tourmind-Booking-MCP)** |
