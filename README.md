# slack-steam-status

A simple Slack app that updates your Slack status to the game you currently play on Steam.

This app uses the [Bolt for Slack](https://slack.dev/bolt/concepts) framework.

Based on [slack-steam-status](https://github.com/pichsenmeister/slack-steam-status) by [David Pichsenmeister](https://github.com/pichsenmeister).

## Steam configuration

1. Get a Steam API key [here](https://steamcommunity.com/dev/apikey)
2. Get your numeric Steam ID 
  - Add your `STEAM_API_KEY` to the `.env` file and call `/id?username=<your Steam display name>`

## Slack app configuration

1. Create an [app](https://api.slack.com/apps) on Slack
2. Add `User Token Scopes` in `OAuth & Permissions`
  - `users.profile:read`
  - `users.profile:write`
3. Install App

## Run the app

### Environment variables

| Variable | Required | Description |
| --- | --- | --- |
| `SLACK_USER_TOKEN` | Yes | Slack user token with profile read/write scopes |
| `SLACK_SIGNING_SECRET` | Yes | Slack app signing secret |
| `STEAM_API_KEY` | Yes | Steam Web API key |
| `STEAM_ID` | Yes | Your numeric Steam ID |
| `POLL_INTERVAL` | No | Polling interval in seconds (default `60`). Set to `0` to disable |
| `PORT` | No | HTTP port (default `3000`) |

For local development, copy `.env.example` to `.env` and fill in your values. The app loads `.env` only when `NODE_ENV` is not `production`. Docker and Kubernetes should inject these as environment variables instead.

### Local (Node.js)

1. Install dependencies via `npm` or `yarn`
2. Copy `.env.example` to `.env` and set the required values
3. Run `npm start` or `npm run dev`

### Docker Compose

```bash
cp .env.example .env
# edit .env with your credentials

docker compose up -d --build
```

The service listens on port 3000. Use `/health` for a no-op health check and `/ping` to trigger a manual status update.

### Helm (Kubernetes / k3s)

Build and load the image into your cluster (or push to a registry your cluster can pull from):

```bash
docker build -t slack-steam-status:latest .
# k3s example:
docker save slack-steam-status:latest | sudo k3s ctr images import -
```

Create a secret with your credentials:

```bash
kubectl create namespace slack-steam-status

kubectl create secret generic slack-steam-status \
  --namespace slack-steam-status \
  --from-literal=SLACK_USER_TOKEN='xoxp-...' \
  --from-literal=SLACK_SIGNING_SECRET='...' \
  --from-literal=STEAM_API_KEY='...' \
  --from-literal=STEAM_ID='...'
```

Install the chart:

```bash
helm install slack-steam-status ./helm/slack-steam-status \
  --namespace slack-steam-status
```

The chart defaults to `secret.existingSecret: slack-steam-status` and runs a single replica. To have Helm create the secret instead (not recommended for production), set `secret.create: true` and pass the credential values.

Optional port-forward for setup or manual triggers:

```bash
kubectl port-forward -n slack-steam-status svc/slack-steam-status 3000:3000
curl http://localhost:3000/id?username=<your-steam-name>
```

## Custom Emoji (Optional)

By default, the app uses a `:steam:` emoji for your game status. If this custom emoji is not available in your Slack workspace, the app will automatically fall back to the `:video_game:` emoji.

To add a custom Steam emoji:
1. Download a Steam logo image (search for "steam logo icon" or use the official Steam branding assets)
2. In Slack, go to your workspace → Customize → Add Custom Emoji
3. Upload the image and name it `:steam:`

## Usage

### Automatic Polling (Recommended)

By default, the app automatically checks your Steam status every minute (60 seconds). You can customize this interval by setting the `POLL_INTERVAL` environment variable in your `.env` file:

```bash
POLL_INTERVAL=60   # Check every minute (in seconds)
POLL_INTERVAL=300  # Check every 5 minutes (in seconds)
```

To disable automatic polling, set `POLL_INTERVAL=0`. The app will then only update your status when the `/ping` endpoint is called.

### Manual Triggering

The app also exposes a `/ping` endpoint that manually checks your current Steam status and updates your Slack profile. You can use this with external services like cron jobs or monitoring tools:

```bash
curl http://localhost:3000/ping
```

**Note:** Make sure your Steam profile is set to "Online" (not "Invisible" or "Offline") for the app to detect your game activity.
