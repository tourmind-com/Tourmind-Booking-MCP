# TourMind Booking MCP Product Packages

This repository contains the ToB and ToC product definitions and their user-installable companion Skills for TourMind Booking MCP. It intentionally does not contain MCP server source code, deployment scripts, or runtime-specific implementation.

## Package contents

```text
Tourmind-Booking-Mcp/
├── README.md
├── TourMind MCP-FORMAT.md
├── TourMind MCP-TOC-FORMAT.md
└── skill/
    ├── tourmind-booking/
    │   ├── SKILL.md
    │   └── references/
    │       └── parameter_guide.md
    └── hotel-booking-ai/
        ├── SKILL.md
        └── references/
            └── parameter_guide.md
```

- `skill/tourmind-booking/` is the ToB companion Skill. It connects to `https://api.tourmind.com/mcp/tob`; the MCP connection supplies its bearer credential.
- `skill/hotel-booking-ai/` is the ToC companion Skill. It connects publicly to `https://api.tourmind.com/mcp/toc`; discovery and rates are public, while order operations use `user_key`.
- `TourMind MCP-FORMAT.md` defines the ToB connection and capability contract.
- `TourMind MCP-TOC-FORMAT.md` defines the ToC connection and capability contract.

## Product model

Both MCP services provide the same hotel discovery, ranking, price verification, booking, display, and order-management capabilities. Their companion Skills intentionally share the same workflow language wherever the product behavior is identical.

The variants differ only where the products differ:

| Concern | ToB | ToC |
|---|---|---|
| MCP endpoint | `https://api.tourmind.com/mcp/tob` | `https://api.tourmind.com/mcp/toc` |
| Connection authentication | Bearer credential in MCP headers | Public connection; no headers |
| Public hotel workflow | Authenticated through the connection | No `user_key` required |
| Booking/order authentication | MCP connection credential | `user_key` from `user_key.txt` in protected tool arguments |
| Read-only `web_url` | Returned through the authenticated connection | Optional; requires an already stored `user_key` on search/rate tools |

The local Skills define the current user workflow and can evolve independently from the stable MCP tool surface.

Each installed `SKILL.md` is the single source of truth for its Skill version:

```markdown
# <Installed companion Skill>

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

The ToB server must satisfy `TourMind MCP-FORMAT.md`; the ToC server must satisfy `TourMind MCP-TOC-FORMAT.md`. Transport framework, hosting, deployment, internal forwarding, and release infrastructure are development decisions and are outside this package.
