# Email Verifier MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-email-verifier)](https://smithery.ai/servers/mambabuilt/mcp-email-verifier) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-email-verifier/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-email-verifier) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-email-verifier%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-email-verifier&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-email-verifier)](https://www.npmjs.com/package/@mambalabsdev/mcp-email-verifier) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-email-verifier)](https://www.npmjs.com/package/@mambalabsdev/mcp-email-verifier) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-email-verifier)](https://github.com/mambalabsdev/mcp-email-verifier/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-email-verifier)

An MCP server for the Mamba Labs Email Verifier actor on Apify. Give it a list of email addresses and it checks each one with the verification service you already pay for: ZeroBounce, NeverBounce, MillionVerifier, BounceBan, or Reoon Email Verifier. You supply the service key.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your key](#your-key)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

- **One flat row per address**: `verdict` (valid, invalid, catch_all, risky, or unknown), the service's own `provider_status` and `provider_sub_status`, `is_role`, `is_free_email`, `is_disposable`, and `did_you_mean` for a spelling fix.
- **One layout for every service**: the five services spell their answers differently; every row uses the same five verdicts and the same columns.
- **Credits protected**: an address with broken syntax is never sent, a repeat is checked once, and a rejected key or an empty service balance stops the run from sending more.

Bad input returns one free row whose `error_reason` starts with `input_invalid:`. With no key set, the tool refuses before any run starts, so nothing is charged.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer, an Apify account with an API token, and an API key for one of the five services.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-email-verifier": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-email-verifier"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token",
        "EMAIL_VERIFIER_API_KEY": "your-verification-service-key"
      }
    }
  }
}
```

Restart the client. The `verify_emails` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.
- Your own API key for ZeroBounce, NeverBounce, MillionVerifier, BounceBan, or Reoon Email Verifier, set as `EMAIL_VERIFIER_API_KEY`.

## Example prompts

- "Verify these 40 addresses with my NeverBounce key and list the ones that are not valid."
- "Which of these leads are catch all or role addresses?"
- "Check jane@example.com and tell me if the domain looks like a typo."

## Tool

`verify_emails`. One tool. Example arguments:

```json
{
  "emails": [
    "jane@example.com",
    "sales@example.org"
  ],
  "provider": "neverbounce"
}
```

## Inputs

Every buyer facing input of the actor except `api_key`, which comes from `EMAIL_VERIFIER_API_KEY` in the server environment so it never passes through the conversation. Omitted inputs take the actor's own defaults. Numbers are sent as strings.

| Input | Type | Description |
|---|---|---|
| `emails` | array | Required. The addresses to verify, 1 to 5,000 per run |
| `email` | string | One more address, added to `emails` |
| `provider` | string | zerobounce (default), neverbounce, millionverifier, bounceban, or reoon. The service your key belongs to |
| `concurrency` | string | Addresses checked at the same time, 1 to 20. Default 5 |
| `max_charge_usd` | string | Optional. The run stops before an actor charge that would pass this amount |

## Pricing

Two separate bills. Your verification service bills each check to your account there, at your plan's price. The actor charges per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `email-verified`, one address your service answered valid, invalid, catch all, or risky | $0.0016 | $0.00152 | $0.00144 | $0.00136 |
| `apify-actor-start`, per run start | $0.0006 | $0.0006 | $0.0006 | $0.0006 |

Unknown answers, error rows, addresses with broken syntax, repeats in the same run, and refused input are free. Mamba Labs pays the Apify platform costs of the run.

## Your key

The key is read from this server's environment and passed in the run input as a secret field. The actor sends it only to the service you chose and never logs, stores, or returns it. Apify blocks the outbound mail port, so every mailbox check runs at your service.

## Full actor documentation

[apify.com/mambalabs/email-verifier](https://apify.com/mambalabs/email-verifier)

## License

MIT. See [LICENSE](LICENSE).
