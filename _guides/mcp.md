---
title: MCP server (AI assistants)
nav_order: 14
description: Connect Claude, ChatGPT or any MCP-compatible AI assistant to Makeplans — endpoint, authentication, available tools and client setup.
---

Makeplans provides an [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server so AI assistants such as Claude and ChatGPT — and any other MCP-compatible client or agent — can work with your Makeplans account: check availability, list services and events, look up customers, and create or cancel bookings.

MCP is an open standard, so there is no Makeplans-specific plugin to install. Point any MCP client at your account's endpoint and authenticate with your API-Key.

The MCP server is for you, the business — it connects with your own credentials and has the same access as the private API. It is not something your end-customers connect to.

## Endpoint

```
POST https://youraccount.makeplans.com/api/mcp
```

For test accounts: `https://youraccount.test.makeplans.net/api/mcp`.

The server uses stateless Streamable HTTP (JSON responses, no SSE), which all current MCP clients support.

## Authentication

Same as the [private API](/guide/authentication/): first enable the API on the account, then send your API-Key as a Bearer token.

```
Authorization: Bearer APIKEY
```

To revoke an assistant's access, disable the API in your account settings, or re-enable it to generate a new API-Key — note that either also affects other integrations using the same key.

## Available tools

| Tool | Description |
|------|-------------|
| `find_available_slots` | Find open slots for a service in a date range |
| `list_services` | List the services you offer |
| `list_events` | List your events |
| `list_bookings` | List bookings (upcoming, date range, state) |
| `get_booking` | Get a single booking by id |
| `find_customer` | Search customers by name |
| `create_booking` | Book a customer in |
| `cancel_booking` | Cancel a booking |

Tool inputs and outputs follow the corresponding [API endpoints](/endpoints/), so the API reference applies to tool results too. The tool set is task-shaped rather than one tool per endpoint, and we expand it based on what customers ask for — anything the assistant cannot do yet, you can already build with the full API.

## Client setup

### Claude Code

```
claude mcp add --transport http makeplans https://youraccount.makeplans.com/api/mcp \
  --header "Authorization: Bearer APIKEY"
```

### Claude Desktop / other MCP clients

Add a remote MCP server with:

* **URL:** `https://youraccount.makeplans.com/api/mcp`
* **Authorization header:** `Bearer APIKEY`

Consult your client's documentation for where remote MCP servers are configured. Any client that supports remote servers over Streamable HTTP with a custom Authorization header will work.

## Notes

* Standard API [rate limits](/guide/rate-limiting/) apply.
* Assistants chain tools: a request like "reschedule Anna's massage to Friday" typically calls `list_bookings`, `find_available_slots`, `cancel_booking` and `create_booking` in sequence.
* Errors follow the [API error format](/guide/errors/) and are returned as tool errors, so assistants can read and act on them.
