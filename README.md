# Resort Manager System Status (Upptime Engine)

This repository powers automated uptime monitoring, incident management, and status page generation for **Resort Manager** using [Upptime](https://upptime.js.org) and [Globalping](https://globalping.io) by jsDelivr.

It executes automated synthetic health checks across the 5 core Resort Manager services from 4 key operating regions:
* **London, UK** (Corporate HQ & Cloud Core)
* **Bangkok, Thailand** (Indochina Regional Gateway)
* **Singapore** (Asia-Pacific Primary Hub & Read Replicas)
* **Jakarta, Indonesia** (Bali & Southeast Asia Edge)

---

## Architecture & How It Works

1. **GitHub Actions Engine**: Pings all endpoints every 5 minutes (`*/5 * * * *`).
2. **Globalping Integration**: Pings originate directly from edge probes located inside data centers and ISPs in London, Bangkok, Singapore, and Jakarta.
3. **Git Time-Series Database**: Commit logs, response time metrics, and uptime percentages are version-controlled in this repository.
4. **Automated Incident Management**: If an endpoint fails consecutive checks, Upptime automatically creates a GitHub Issue to track the incident, and closes it when the endpoint recovers.
5. **Static Status Dashboard**: Automatically publishes a fast, responsive status website at `https://status.resort-manager.com` via GitHub Pages.

---

## Quick Setup Instructions (2 Minutes)

### Step 1: Create the GitHub Repository
1. Go to the official Upptime template: **[github.com/upptime/upptime](https://github.com/upptime/upptime)**
2. Click **"Use this template"** > **"Create a new repository"**.
3. Repository name: `ResortManager.Status` (under `MarkLFT` or your organisation).
4. Set repository visibility to **Public** (required for free GitHub Actions minutes and free GitHub Pages).

### Step 2: Add the `.upptimerc.yml` Configuration
Copy the `.upptimerc.yml` file from this folder into the root of your newly created `ResortManager.Status` repository. It is already fully customized with:
- All 5 core services (RM-Flex, RM-Desk, RM-WiFi, RM-Tables, RM-CRM)
- All 4 Globalping probe locations (London, Bangkok, Singapore, Jakarta)
- Resort Manager branding, logos, and custom navigation

### Step 3: Create GitHub Secret (`GH_PAT`)
Upptime requires a Personal Access Token to commit status history and manage incident issues:
1. Go to **GitHub Settings** > **Developer Settings** > **Personal Access Tokens (Tokens classic)**.
2. Click **"Generate new token (classic)"**.
3. Note: `Upptime Status CI`.
4. Expiration: `No expiration` (or 1 year).
5. Scopes to check:
   - [x] **`repo`** (Full control of private/public repositories)
   - [x] **`workflow`** (Update GitHub Action workflows)
6. Copy the generated token (`ghp_...`).
7. In your `ResortManager.Status` repository, go to **Settings** > **Secrets and variables** > **Actions**.
8. Click **"New repository secret"**:
   - Name: `GH_PAT`
   - Secret: *paste the token*
9. Click **Add secret**.

### Step 4: Enable GitHub Pages & Custom Domain
1. In repository **Settings** > **Pages**:
   - **Source**: `Deploy from a branch`
   - **Branch**: `gh-pages` / `/ (root)` (Note: Upptime creates the `gh-pages` branch after its first run).
   - **Custom domain**: `status.resort-manager.com`
2. In your DNS (Cloudflare / Route 53):
   - Add a `CNAME` record: `status` pointing to `marklft.github.io` (with DNS only / grey-cloud in Cloudflare).

---

## Services Monitored Across Regions

| Service | London | Singapore | Bangkok | Jakarta |
| :--- | :---: | :---: | :---: | :---: |
| **RM-Flex** (Cloud PMS & Sync Broker) | `london` | `singapore` | `bangkok` | `jakarta` |
| **RM-Desk** (Ticketing & AI Routing) | `london` | `singapore` | — | — |
| **RM-WiFi** (RADIUS & Captive Portal) | — | `singapore` | `bangkok` | `jakarta` |
| **RM-Tables** (Reservations & Concierge)| — | `singapore` | `bangkok` | `jakarta` |
| **RM-CRM** (Guest Profile Engine) | `london` | `singapore` | — | — |

---

## Manual Endpoint Verification

To test a Globalping probe manually from your terminal at any time:
```bash
# Using the Globalping CLI or curl:
curl -X POST https://api.globalping.io/v1/measurements \
  -H "Content-Type: application/json" \
  -d '{"type":"http","target":"api.resort-manager.com","locations":[{"city":"Bangkok"}]}'
```
