# Contributing to Tavern

Thanks for wanting to pull up a chair! 🍺 This guide explains how to propose changes to any repository in the [Tavern](https://github.com/tavern-bot) organization.

> [!NOTE]
> Tavern is **proprietary, closed-source software**. Contributions are accepted only from people invited to the organization. If you'd like to help, contact the maintainer first. Before contributing, read the [license](LICENSE.md), in particular its *Contributions* section.

By taking part, you agree to follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Table of contents

- [Ways to contribute](#-ways-to-contribute)
- [Before you start](#-before-you-start)
- [Development setup](#-development-setup)
- [Project structure](#-project-structure)
- [Coding guidelines](#-coding-guidelines)
- [Translations](#-translations)
- [Writing for the characters](#-writing-for-the-characters)
- [Tests and acceptance criteria](#-tests-and-acceptance-criteria)
- [Commits and pull requests](#-commits-and-pull-requests)
- [Lines we don't cross](#-lines-we-dont-cross)

## 🧭 Ways to contribute

- **Report a bug** with the [bug report form](https://github.com/tavern-bot/tavern/issues/new?template=bug_report.yml)
- **Suggest a feature** with the [feature request form](https://github.com/tavern-bot/tavern/issues/new?template=feature_request.yml)
- **Translate** Tavern into your language (see [Translations](#-translations))
- **Write code**: fix a bug or build a feature that already has an approved issue
- **Improve the docs**: fix a typo, clarify a setup step, add an example

> [!IMPORTANT]
> Security vulnerabilities must **never** be reported in a public issue. Follow the [security policy](SECURITY.md) instead.

## 🚦 Before you start

1. **Look for an existing issue** before opening a new one.
2. **Discuss before you build.** For anything bigger than a small bug fix, open an issue (or comment on an existing one) and wait for a maintainer to confirm the approach. That way your work won't be turned down because of a design disagreement.
3. **Claim the issue** by commenting on it, so two people don't work on the same thing.
4. Issues labeled `good first issue` are a good place to start.

## 💻 Development setup

### Prerequisites

| Tool | Version | Used for |
| --- | --- | --- |
| [Node.js](https://nodejs.org/) | Current LTS | Bot and dashboard |
| [Docker](https://www.docker.com/) + Docker Compose | Recent | PostgreSQL, Redis and Lavalink locally |
| [Git](https://git-scm.com/) | Recent | Version control |

You will also need a **test Discord application** of your own. Never use the production bot token.

1. Create an application in the [Discord Developer Portal](https://discord.com/developers/applications) and add a bot to it.
2. Enable the **Server Members** intent. Only enable **Message Content** if you are working on AutoMod or content logs.
3. Invite the bot to a **private test server** that you own.

### Getting the code running

```bash
# 1. Clone the repository (you need to be a member of the organization)
git clone https://github.com/tavern-bot/tavern.git
cd tavern

# 2. Copy the example environment file and fill in your test bot credentials
cp .env.example .env

# 3. Start the local services (PostgreSQL, Redis, Lavalink)
docker compose up -d
```

The exact install, migration and start commands live in the README of each repository, which is kept up to date with the tooling.

> [!CAUTION]
> Never commit `.env`, tokens, webhook URLs or database dumps. If you leak a secret by mistake, reset it in the Developer Portal right away and tell a maintainer.

## 🗂️ Project structure

The main repository is a monorepo:

```
apps/
  bot/          Discord client, one folder per module (moderation/, economy/, music/…)
  dashboard/    Web dashboard
packages/
  db/           Prisma schema and migrations
  shared/       Types, constants and translation files
```

Each module exposes its own **commands**, **events** and **settings**. A new feature belongs in the module it extends, and code shared by several modules goes in `packages/shared`.

## 📐 Coding guidelines

### General

- **TypeScript in strict mode.** Avoid `any`, and use `unknown` and narrow it when needed.
- **Linting and formatting run in CI.** Run them locally before pushing, because a red pipeline blocks the merge.
- **Keep changes focused.** One pull request should do one thing. Unrelated refactors go in their own pull request.

### Discord-specific rules

- **Slash commands only.** No text-prefix commands.
- **Answer within 3 seconds.** If an interaction may take longer, `defer` it first, then follow up.
- **Errors are ephemeral, translated and free of stack traces.** Internal errors are logged with an ID that is shown to the user so they can quote it to support.
- **Respect role hierarchy.** No moderation action may target a member whose highest role is equal to or above the moderator's or the bot's, or the server owner.
- **Respect rate limits.** Bulk actions (purge, lockdown, mass role changes) go through the queue.
- **Ask for as little as possible.** Use the fewest intents and permissions the feature needs, and justify any new one in your pull request.
- **Persist anything scheduled.** Tempbans, giveaways, reminders and temporary roles go through the persistent scheduler so they survive a restart. Never use a bare `setTimeout` for these.

### Data and money

- **Every balance change is an atomic database transaction** and writes a `Transaction` record (source, amount, reason). Two fast clicks must never spend twice.
- **Casino randomness is server-side and cryptographically secure.** Outcomes (mine positions, crash point, cards) are fixed on the server when the game starts.
- **Collect as little personal data as possible.** Any new stored field must be covered by the data export and deletion commands.

### Webhooks

- Webhook tokens are **stored encrypted** and must **never** appear in logs, errors or API responses.
- Persistent messages (role panels, ticket panels, rules) are always sent by the bot itself, never through a webhook.
- Every webhook code path needs a fallback: if the webhook can't be created, send a normal bot message carrying the character's embed.

## 🌍 Translations

**English is the reference language.** All code, comments, documentation and user-facing text are written in English first.

- **No hard-coded strings.** Every user-facing text lives in the translation files in `packages/shared`.
- **French must stay at 100%.** A feature that adds English keys also adds the French ones in the same pull request. CI fails if a French key is missing.
- **Other languages** are handled through our community translation platform. A language is published once it reaches 90% coverage.
- **Missing keys fall back to English.** A raw key must never reach a user.
- **Command names and descriptions** are localized too, so members see them in their own Discord language.

Want to translate? Open a [translation request](https://github.com/tavern-bot/tavern/issues/new?template=translation.yml) and we'll get you set up.

## 🎭 Writing for the characters

Each module is voiced by a character: Bram, Grimm, Mira, Lucky Jack, Fennel, Quill, Pip or Madame Rosa. When you write user-facing text:

- **Use the character that owns the module**, and keep their texts under their own key in the translation files.
- **Stay in voice.** Grimm is terse, Mira is lively, Fennel loves a rhyme. Each character has a reference sheet for tone, verbal tics and things to avoid.
- **Write a Classic version.** Every text needs a neutral variant for servers that turn on Classic mode.
- **Keep sanction DMs neutral.** They are plain and factual, with no personality, whatever the mode.
- **Adapt jokes, don't translate them literally.** Translators may rewrite a pun so it works in their language.

## ✅ Tests and acceptance criteria

Features are specified as user stories with IDs such as `US-MOD-04` or `US-ECO-12`. Each ✅ acceptance criterion is a test case.

- **Link the user story** your pull request implements (for example `Implements US-MOD-04`).
- **Every acceptance criterion needs a test**, either a unit test or a Gherkin scenario for integration tests. A feature is done when all its criteria pass.
- **Business logic needs unit tests**, especially economy, sanctions, casino games and permissions.
- **Bug fixes come with a regression test** that fails without the fix.
- **Run the tests locally** before asking for a review.

Some behavior can only be checked by hand on a test server (voice, webhooks, rate limits). If so, describe what you tested in the pull request.

## 📝 Commits and pull requests

### Branches

Branch from `main` and use a descriptive name:

```
feat/economy-weekly-streak
fix/moderation-tempban-restart
docs/contributing-setup
i18n/fr-music-module
```

### Commit messages

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short summary in the imperative mood>
```

| Type | When |
| --- | --- |
| `feat` | A new feature |
| `fix` | A bug fix |
| `i18n` | Translations only |
| `docs` | Documentation only |
| `test` | Adding or fixing tests |
| `refactor` | A code change that neither fixes a bug nor adds a feature |
| `perf` | A performance improvement |
| `chore` / `ci` | Tooling, dependencies, pipelines |

The scope is usually the module: `moderation`, `economy`, `casino`, `music`, `levels`, `welcome`, `roles`, `tickets`, `community`, `utilities`, `dashboard`, `db`, `core`.

```
feat(casino): add cash-out button to crash game
fix(moderation): resume tempbans after a restart
i18n(music): add French translations for queue commands
```

### Pull requests

1. Keep your branch up to date with `main`.
2. Fill in the pull request template completely.
3. Make sure CI is green.
4. A maintainer will review it. Please be patient, because Tavern is maintained on volunteer time.
5. Pull requests are **squash-merged**, so the title must follow the Conventional Commits format.

## 🚫 Lines we don't cross

Tavern has a few hard rules, mostly for legal and platform reasons. Pull requests that break them will be closed, however well written:

- **No YouTube playback.** It breaks YouTube's Terms of Service, which is why other music bots were shut down. Spotify, Deezer and Apple Music links are used for metadata only, never for streaming their audio.
- **No real money anywhere near the economy.** No selling currency or chips, no premium plan that grants currency or casino advantages, no conversion to money, gift cards or anything else of value. The currency is fictional and must say so.
- **No storing or downloading audio files.**
- **No features that break [Discord's Terms of Service](https://discord.com/terms) or [Developer Policy](https://discord.com/developers/docs/policies-and-agreements/developer-policy)**, such as self-bots, scraping or mass DMs.

## 📄 License

Tavern is proprietary software. By contributing, you grant the copyright holder the rights described in the *Contributions* section of the [license](LICENSE.md), including the right to release your contribution under a different license later. Never copy code into Tavern from a source whose license doesn't allow it.

---

Questions? See [SUPPORT.md](SUPPORT.md). Thanks for helping build Tavern! 🍻
