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

The MCP service provides stable hotel tools. The local Skill defines the current user workflow and can evolve independently as search, ranking, price verification, booking, and display strategies improve.

The installed `SKILL.md` is the single source of truth for the Skill version:

```markdown
# TourMind Booking Skill

**Skill version:** `<current-version>`
```

The Agent sends this version as `current_version` only when calling the dedicated `check_skill_update` tool. Hotel search, rate, booking, order, cancellation, and payment tools do not carry a Skill version. Users do not maintain a Skill version in the MCP connection configuration.

## User update experience

The Agent calls `check_skill_update` once when the Skill is first used in every new conversation, and again when an existing conversation resumes after at least 24 hours of inactivity. When that tool returns a visible `skill_update`:

1. Complete the user's current hotel request first.
2. Show the release message and explain that updating is recommended for TourMind's latest hotel-search and price-query strategy because older endpoints may become unavailable.
3. Offer to update from the official sources listed by `release_source_url`.
4. Obtain confirmation before changing local Skill files.
5. Update the Skill and its version declaration together.
6. Validate that the installed version matches `latest_version`.
7. Use the new version in the next scheduled update check without asking the user to edit the MCP connection.

## Development handoff

The server implementation only needs to satisfy the product contract in `TourMind MCP-FORMAT.md`. Transport framework, hosting, deployment, internal forwarding, and release infrastructure are development decisions and are outside this package.

## TourMind hotel booking ecosystem

Choose the package that matches the audience and connection model:

| Audience | Integration | Authentication model | Repository |
|---|---|---|---|
| Consumer / ToC | Direct HTTP Skill | Public search and availability; `user_key` only for order operations | [Hotel Booking AI](https://github.com/tourmind-com/Hotel-Booking-AI) |
| Business / ToB | Direct HTTP Skill | Skill Token required for every API call | [TourMind Booking Skill](https://github.com/tourmind-com/Tourmind-Booking-Skill) |
| Consumer / ToC | MCP package + companion Skill | Public MCP connection; `user_key` only for order operations | [Hotel Booking AI MCP](https://github.com/tourmind-com/Hotel-Booking-AI-MCP) |
| Business / ToB | MCP package + companion Skill | Bearer-authenticated MCP connection | **[TourMind Booking MCP](https://github.com/tourmind-com/Tourmind-Booking-MCP)** |
