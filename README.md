# GTM Server-Side Tagging on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/gtm-server-tagging)

Deploy and host Google Tag Manager Server-Side Tagging (sGTM) on Railway. This image is a thin wrapper around Google's official `gcr.io/cloud-tagging-10302018/gtm-cloud-image:stable`.

## What is sGTM?

Server-side tagging moves tag execution from the browser to a server you control:

- **Performance** — fewer scripts in the browser, faster page loads
- **Control** — you decide which data leaves your infrastructure
- **Resilience** — first-party tags aren't blocked by ad/tracking blockers
- **Privacy** — sensitive data stays on infrastructure you own

## Architecture

A production sGTM setup runs **two services** from the same image:

| Service | Purpose | Instances |
|---|---|---|
| **Tagging server** | Handles live traffic, runs your tags | Scale horizontally (≥2) |
| **Preview server** | Powers GTM's debug/preview mode | Exactly 1 (do not autoscale) |

The tagging server points at the preview server via `PREVIEW_SERVER_URL`. Without a preview server you can still serve traffic, but you can't debug containers from the GTM UI.

## Deploy

### 1. Create the preview server

Deploy this image as a Railway service with:

| Variable | Value |
|---|---|
| `CONTAINER_CONFIG` | Your server container config string from GTM (Admin → Container Settings) |
| `RUN_AS_PREVIEW_SERVER` | `true` |

Generate a Railway public domain for it (e.g. `gtm-preview.up.railway.app`). Keep this service at **1 replica**.

### 2. Create the tagging server

Deploy a second service from this same image with:

| Variable | Value |
|---|---|
| `CONTAINER_CONFIG` | Same value as the preview server |
| `PREVIEW_SERVER_URL` | `https://` URL of the preview service from step 1 |

Scale this service to ≥2 replicas for redundancy.

### 3. Set up a first-party domain

Point a subdomain of your site (e.g. `tags.example.com`) at the tagging server. First-party hosting is what unlocks the resilience benefits — using a Railway-provided domain works but defeats the point. Add the same URL in GTM under **Admin → Container Settings → Server container URL**.

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `CONTAINER_CONFIG` | Yes | Server container configuration string from GTM |
| `RUN_AS_PREVIEW_SERVER` | Preview only | Set to `true` on the preview service |
| `PREVIEW_SERVER_URL` | Tagging only | HTTPS URL of the preview service |
| `PORT` | No | Listening port (default: `8080`) |
| `GOOGLE_CLOUD_PROJECT` | No | GCP project ID — needed if tags write to BigQuery / Firestore |
| `GOOGLE_APPLICATION_CREDENTIALS` | No | Path to a mounted service account JSON for GCP access |

## Health Checks

The image exposes `GET /healthy` which returns `200` when the server is ready. Configure Railway's health check path to `/healthy` so failed containers are recycled automatically.

## Sizing & Scaling

Google recommends:

- **At most 1 vCPU per container** — scale by adding replicas, not by giving one container more cores
- **Load balancer timeout > 20s** for slow third-party tag responses
- Restart containers periodically to pick up base-image security updates (Railway redeploys handle this)

## Validation

1. In GTM, open your server container's **Preview** mode
2. Send a test event from your site or Tag Assistant
3. The request should appear in the preview UI within a few seconds

If preview never connects, double-check `PREVIEW_SERVER_URL` on the tagging service and that the preview service is reachable over HTTPS.

## Links

- [GTM Server-Side Tagging docs](https://developers.google.com/tag-platform/tag-manager/server-side)
- [Manual setup guide](https://developers.google.com/tag-platform/tag-manager/server-side/manual-setup-guide)
- [Railway templates](https://docs.railway.com/templates)
