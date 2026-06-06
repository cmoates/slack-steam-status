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

1. Install dependencies via `npm` or `yarn`
2. Create a `.env` file and with following keys
  - `SLACK_USER_TOKEN=<your Slack app's user token>`
  - `SLACK_SIGNING_SECRET=<your Slack app's signing secret>`
  - `STEAM_API_KEY=<your Steam API key>`
  - `STEAM_ID=<your numeric Steam ID>`
  - `POLL_INTERVAL=<optional: polling interval in seconds, default 60 (1 minute)>`

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
