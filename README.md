# Orbit — Discord Server Management Bot

> A powerful, modular, all-in-one Discord server management bot built with **Discord.js v14** and **MongoDB**.
> Made by **T-Rex Development**.

Orbit is designed to be the only bot your server needs — moderation, giveaways, timers, utilities, fun commands, autoroles, autoresponders, custom prefixes, no-prefix users, voice controls, and more. Everything is loaded dynamically through a clean handler system, so adding new commands or events is as simple as dropping a file in the right folder.

---

## Table of Contents

- [Features](#features)
- [Command Categories](#command-categories)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Inviting the Bot](#inviting-the-bot)
- [Custom Prefix & No-Prefix System](#custom-prefix--no-prefix-system)
- [Adding New Commands](#adding-new-commands)
- [Adding New Events](#adding-new-events)
- [Troubleshooting](#troubleshooting)
- [Credits](#credits)
- [Support the Project — Buy Me a Coffee](#support-the-project--buy-me-a-coffee)
- [License](#license)

---

## Features

- **Discord.js v14** with the new Components V2 (Containers, Sections, Separators, Text Displays).
- **MongoDB persistence** via Mongoose for all settings, giveaways, timers, autoresponders, autoroles, ignored channels, custom prefixes, no-prefix users, maintenance state, and reminders.
- **Dynamic command & event handler** — drop a file into `commands/<category>/` or `events/` and it loads automatically.
- **Three trigger modes** for every command:
  1. Default prefix (configurable per-guild, falls back to `&`).
  2. Bot mention as a prefix (`@Orbit help`).
  3. No-prefix mode for whitelisted users (with optional expiry time).
- **Per-guild custom prefix** — store and override the default prefix per server.
- **Owner-only command gating** — protected commands like `reload`, `dm`, `noprefix`, `servercount`, `createinvite`.
- **Channel ignore system** with bypass roles, so command spam can be quarantined.
- **Auto-recovery** — the process keeps running on unhandled rejections and uncaught exceptions; only `SIGINT`/`SIGTERM` trigger a graceful shutdown.
- **Dynamic presence** — auto-refreshes every 10 minutes; toggleable streaming mode.
- **Anti-crash utility** and a colored logger (`utils/antiCrash.js`, `utils/logger.js`).

---

## Command Categories

The bot ships with 11 command categories. Default prefix is `&` (configurable).

### Server
- `prefix` — view or change the per-guild prefix.

### Moderation
`ban`, `unban`, `kick`, `purge`, `lock`, `unlock`, `unlockall`, `hideall`, `unhide`, `unhideall`, `nick`, `nuke`, `poll`, `deletechannel`, `maintenance` (alias `mt`).

### Information
`help` (interactive menu with select-menu + buttons), `invite`, `support`, `serverinfo`, `userinfo`, `stats`, `uptime`, `vote`, `custombutton`.

### Utility
`avatar`, `banner`, `ignore` (channel-ignore manager), `invoice`, `list` (emojis / bots / roles / boosters / bans), `memberCount`, `ping`, `reminder`, `rename`, `report`, `roleicon`, `steal`.

### Fun
`hug`, `kiss`, `slap`, `mines`, `snake`.

### Giveaway
`gstart`, `gend`, `greroll`, `glist` — full giveaway lifecycle with reaction-based entry, automatic winner selection, and reroll.

### Timer
`tstart`, `tpause`, `tresume`, `tend` — countdown timers stored in DB and rendered with Components V2.

### Voice
`vcmute`, `vcunmute`, `vcmuteall`.

### Welcome
`autorole` — configure separate human/bot autoroles per guild (`add`, `remove`, `list`, `reset`).

### Autoresponder
`autoresponder` — trigger-based replies, optional embed/footer, and emoji reactions.

### Owner
`reload`, `dm`, `createinvite`, `noprefix`, `servercount` — bot-owner-only utilities.

> Run `&help` (or whatever your prefix is) in your server for the interactive command menu.

---

## Project Structure

```
Orbit-/
├── client/
│   └── botClient.js           # Discord client setup + intents + handler bootstrap
├── commands/
│   ├── autoresponder/
│   ├── fun/
│   ├── giveaway/
│   ├── information/
│   ├── moderation/
│   ├── owner/
│   ├── server/
│   ├── timer/
│   ├── utility/
│   ├── voice/
│   └── welcome/
├── data/                      # Mongoose models + JSON stores
│   ├── Autoresponder.js
│   ├── Autorole.js
│   ├── Giveaway.js
│   ├── Ignore.js
│   ├── Maintenance.js
│   ├── Noprefix.js
│   ├── Prefix.js
│   ├── Reminder.js
│   ├── Timer.js
│   └── welcomeConfig.json
├── events/                    # All Discord events (auto-loaded)
│   ├── ClientReady.js
│   ├── clientTag.js
│   ├── guildCreate.js
│   ├── guildMemberAdd.js
│   ├── interactionCreate.js
│   ├── messageCreate.js       # Prefix/mention/no-prefix routing + ignore system
│   ├── noprefix.js
│   └── timerChecker.js
├── handlers/
│   ├── commandHandler.js      # Loads commands/<category>/*.js
│   └── eventHandler.js        # Loads events/*.js
├── utils/
│   ├── antiCrash.js
│   └── logger.js
├── config.json                # Token, prefix, owner, mongoURI, etc.
├── index.js                   # Entry point — connects Mongo and logs in
├── package.json
└── README.md
```

---

## Requirements

- **Node.js v18 or higher** (Discord.js v14 requires Node 18+).
- **MongoDB** — either a local instance or a free **MongoDB Atlas** cluster.
- A **Discord Bot Token** from the [Discord Developer Portal](https://discord.com/developers/applications).
- The following **Privileged Gateway Intents** enabled in the developer portal:
  - `MESSAGE CONTENT INTENT`
  - `SERVER MEMBERS INTENT`
- Native build tools (only needed if you use `canvas` / `better-sqlite3`):
  - **Linux:** `build-essential`, `libcairo2-dev`, `libpango1.0-dev`, `libjpeg-dev`, `libgif-dev`, `librsvg2-dev`.
  - **Windows:** `windows-build-tools` or Visual Studio Build Tools + Python.
  - **macOS:** Xcode Command Line Tools.

---

## Installation

```bash
# 1. Clone the repository
git clone https://github.com/dray-me/Orbit-.git
cd Orbit-

# 2. Install dependencies
npm install

# 3. Configure the bot (see next section)
#    Open config.json and fill in your values.

# 4. Start the bot
npm start
```

---

## Configuration

Edit `config.json` in the project root:

```json
{
  "token": "YOUR_BOT_TOKEN",
  "defaultPrefix": "&",
  "ownerID": "YOUR_DISCORD_USER_ID",
  "reportChannelID": "CHANNEL_ID_FOR_BUG_REPORTS",
  "mongoURI": "mongodb+srv://user:pass@cluster.mongodb.net/orbit",
  "supportServer": "https://discord.gg/your-invite",

  "credits": {
    "owner": "Paradox",
    "developers": ["Dray", "Deepjoyti"],
    "supportTeam": ["HellFire"],
    "supportServer": "https://discord.gg/DJxHfWGyr9"
  }
}
```

| Key               | Description                                                                 |
| ----------------- | --------------------------------------------------------------------------- |
| `token`           | Your Discord bot token. **Never commit this to a public repo.**             |
| `defaultPrefix`   | Fallback prefix used when a guild has not set a custom one. Default: `&`.   |
| `ownerID`         | Your Discord user ID. Required for owner-only commands.                      |
| `reportChannelID` | Channel ID where `report` command output is forwarded.                       |
| `mongoURI`        | MongoDB connection string (Atlas or self-hosted).                            |
| `supportServer`   | Invite link displayed by `support` / `invite` commands.                      |

> **Tip:** For production, move `token` and `mongoURI` into a `.env` file and load them with `dotenv` (already in dependencies).

### Getting a Bot Token
1. Go to the [Discord Developer Portal](https://discord.com/developers/applications).
2. Create a **New Application** -> open the **Bot** tab -> **Reset Token** to copy it.
3. Under **Privileged Gateway Intents**, enable **Message Content** and **Server Members**.
4. Paste the token into `config.json`.

### Getting a MongoDB URI
1. Create a free cluster on [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).
2. **Database Access** -> create a user with read/write permissions.
3. **Network Access** -> allow your IP (or `0.0.0.0/0` for any IP).
4. Click **Connect** -> **Drivers** -> copy the connection string.
5. Replace `<password>` and append a database name, then paste into `config.json`.

---

## Running the Bot

```bash
npm start
```

You should see output similar to:

```
Loaded N commands
Loaded N events
Connected to MongoDB | T-Rex Devlopment
Login process initiated | T-Rex Devlopment
Logged in as Orbit#0000
All background services started
```

### Running with a process manager (recommended for production)

Using **PM2**:

```bash
npm install -g pm2
pm2 start index.js --name orbit
pm2 logs orbit
pm2 save
pm2 startup    # auto-restart on system reboot
```

Using **nodemon** (development with auto-reload):

```bash
npm install -g nodemon
nodemon index.js
```

---

## Inviting the Bot

Generate an invite URL from the Developer Portal under **OAuth2 -> URL Generator**:
- **Scopes:** `bot`, `applications.commands`
- **Bot Permissions:** `Administrator` (recommended), or pick the specific permissions you need (Manage Roles, Manage Channels, Kick/Ban Members, Manage Messages, Mute Members, etc.).

Or use this template (replace `CLIENT_ID`):

```
https://discord.com/oauth2/authorize?client_id=CLIENT_ID&permissions=8&scope=bot%20applications.commands
```

---

## Custom Prefix & No-Prefix System

Orbit accepts commands in three ways:

1. **Default / guild prefix** — `&help`, or whatever the guild has configured via the `prefix` command.
2. **Mention as prefix** — `@Orbit help` works in every server.
3. **No-prefix mode** — the bot owner can grant specific users permission to run commands with no prefix at all (`help` instead of `&help`). The owner uses the `noprefix` command, optionally with an expiry time.

The routing logic lives in `events/messageCreate.js` and respects the channel-ignore system (with role-based bypass).

---

## Adding New Commands

1. Create a file under the appropriate category folder, e.g. `commands/utility/myCommand.js`.
2. Export an object with at least `name` and `execute`:

```js
module.exports = {
  name: "mycommand",
  aliases: ["mc"],
  description: "Does something useful",
  ownerOnly: false, // optional
  async execute(message, args, client) {
    await message.reply("Hello from my command!");
  },
};
```

3. Restart the bot (or use the owner-only `reload` command).

The command handler picks up every `.js` file in `commands/<category>/` automatically.

---

## Adding New Events

1. Create a file in `events/`, e.g. `events/myEvent.js`.
2. Export `name`, `execute`, and optionally `once`:

```js
module.exports = {
  name: "guildMemberRemove",
  once: false,
  async execute(member, client) {
    console.log(`${member.user.tag} left ${member.guild.name}`);
  },
};
```

The event handler wires it to the client automatically.

---

## Troubleshooting

| Problem                                  | Fix                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `Used disallowed intents`                | Enable **Message Content** and **Server Members** intents in the Developer Portal.                   |
| `MongoServerError: bad auth`             | Double-check the username/password and that the user has access to the database in your URI.         |
| Bot ignores commands                     | Verify the prefix (`&` by default), that the bot can read messages, and that the channel isn't ignored. |
| `canvas` / `better-sqlite3` build errors | Install the native build tools listed in [Requirements](#requirements).                              |
| Bot crashes on a command                 | Check the console — `unhandledRejection` and `uncaughtException` are logged but won't kill the bot.  |

---

## Credits

- **Owner:** Paradox
- **Developers:** Dray, Deepjoyti
- **Support Team:** HellFire
- **Support Server:** https://discord.gg/DJxHfWGyr9

Built with [discord.js](https://discord.js.org), [mongoose](https://mongoosejs.com), [ms](https://www.npmjs.com/package/ms), [chalk](https://www.npmjs.com/package/chalk), [moment](https://momentjs.com), [dayjs](https://day.js.org), [axios](https://axios-http.com), and [canvas](https://www.npmjs.com/package/canvas).

---

## Support the Project — Buy Me a Coffee

If Orbit has been useful to you and you'd like to support continued development, consider sending a tip in **Bitcoin (BTC)**. Every sat helps keep the lights on and the commits flowing.

**BTC (Bitcoin) wallet address:**

```
bc1qel57phnjm7tz0zpyrwsrhj302lg4dq8h4qxay9
```

You can also scan or copy the address above into your favorite wallet app. Thank you for the support!

> Tip: this is a **native SegWit (bech32)** address — make sure your wallet supports `bc1...` addresses before sending. Always double-check the address before broadcasting a transaction.

---

## License

This project is distributed under the terms of the license in the [LICENSE](./LICENSE) file. Please review it before using the code in your own projects.

---

Made with care by **T-Rex Development**. Pull requests, issues, and ideas are welcome.
