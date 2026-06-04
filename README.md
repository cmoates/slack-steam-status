# slack-steam-status

A simple Slack app that updates your Slack status to the game you currently play on Steam.

This app uses the [Bolt for Slack](https://slack.dev/bolt/concepts) framework.

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

## Custom Emoji (Optional)

By default, the app uses a `:steam:` emoji for your game status. If this custom emoji is not available in your Slack workspace, the app will automatically fall back to the `:video_game:` emoji.

To add a custom Steam emoji:
1. Download the [Steam logo](https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/Steam_icon_logo.svg/512px-Steam_icon_logo.svg.png)
2. In Slack, go to your workspace → Customize → Add Custom Emoji
3. Upload the image and name it `steam`

## Usage

The app exposes a `/ping` endpoint that checks your current Steam status and updates your Slack profile accordingly. Set up a cron job or external service to ping this endpoint regularly (e.g., every 5 minutes) to keep your Slack status in sync with your Steam activity.

**Note:** Make sure your Steam profile is set to "Online" (not "Invisible" or "Offline") for the app to detect your game activity.
