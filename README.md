# GTM Server Tagging on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/gtm-server-tagging)

Deploy and host Google Tag Manager Server-Side Tagging on Railway with one click.

## What is GTM Server-Side Tagging?

Google Tag Manager Server-Side Tagging moves tag processing from the user's browser to a server you control. This gives you:

- **Better performance** — fewer scripts in the browser means faster page loads
- **More control** — you decide what data gets sent to third parties
- **Improved accuracy** — server-side tags aren't blocked by ad blockers
- **Data privacy** — keep sensitive data on your own infrastructure

## How to Deploy

1. Click the **Deploy on Railway** button above
2. Set the required environment variables:
   - `CONTAINER_CONFIG` — Your GTM server container config string (from GTM UI)
3. Deploy and configure your custom domain

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `CONTAINER_CONFIG` | Yes | GTM server container configuration string |
| `PORT` | No | Server port (default: 8080) |

## Links

- [GTM Server-Side Tagging Docs](https://developers.google.com/tag-platform/tag-manager/server-side)
- [Railway Templates Docs](https://docs.railway.com/templates)
