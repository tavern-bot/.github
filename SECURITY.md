# Security Policy

Tavern runs inside Discord servers, holds moderation powers and keeps track of server data. We take its security seriously and appreciate any help in finding and fixing vulnerabilities.

## Supported versions

Tavern is proprietary software in active development and is only run by its maintainers. Only the **hosted bot** and its **web dashboard** receive security fixes. Self-hosting is not permitted.

## Reporting a vulnerability

> [!WARNING]
> **Do not open a public issue, discussion or pull request** for a security problem, and don't share it on Discord.

Report it privately by email to **victor.jost@epitech.eu**, with `[Security]` at the start of the subject line.

### What to include

- The affected component (bot module, dashboard, API) and the date you observed the issue
- A description of the vulnerability and its impact
- Step-by-step instructions to reproduce it, ideally on a test server you own
- A proof of concept, if you have one
- Any suggested fix or mitigation

## What happens next

As a small volunteer team, we aim for the following:

| Step | Target time |
| --- | --- |
| Acknowledge your report | Within **3 days** |
| Confirm the issue and assess its severity | Within **10 days** |
| Fix critical and high-severity issues | As fast as possible, usually within **30 days** |

We'll keep you updated as we go, agree on a disclosure date with you, and credit you in the security advisory unless you'd rather stay anonymous.

## Scope

### In scope

- **Permission bypass**: running a command without the required role, channel or Discord permission
- **Role hierarchy bypass**: sanctioning a member with an equal or higher role, or the server owner
- **Cross-server data leaks**: reading or changing another server's settings, cases, balances or tickets
- **Dashboard flaws**: broken OAuth2 flow, session hijacking, CSRF, XSS, access to a server without *Manage Server*, IDOR
- **Webhook flaws**: leaking webhook tokens or URLs, impersonating a character, getting around impersonation detection
- **Economy integrity**: duplicating currency, spending twice, bypassing bet limits, predicting or influencing casino outcomes
- **Protection bypass**: getting around anti-raid, anti-nuke, captcha verification or AutoMod in a way that weakens the protection itself
- **Secret exposure**: tokens, keys or credentials in logs, errors, responses or the repository
- **Injection**: SQL injection, command injection, template or variable injection (for example in custom commands or welcome messages)
- **Personal data**: anything that undermines the data export and deletion features

### Out of scope

- Vulnerabilities in Discord itself (report them to [Discord's bug bounty program](https://discord.com/security))
- Denial of service, load testing or spam against the hosted bot or dashboard
- Social engineering of maintainers or users, and physical attacks
- Missing security headers or best practices with no demonstrated impact
- Rate limiting on endpoints that don't change data
- Automated scanner output that hasn't been verified

## Rules of engagement

While researching, please:

- **Only test on servers you own** or have explicit permission to test on
- **Don't access, change or delete other people's data.** If you reach any by accident, stop and tell us.
- **Don't degrade the service** for other users
- **Give us reasonable time to fix the issue** before disclosing it publicly

If you follow this policy in good faith, we will consider your research authorized, won't take legal action against you, and will work with you to understand and fix the problem.

## For server admins

- Grant Tavern only the permissions its enabled modules need.
- Limit the *Manage Webhooks* permission to administrators, so nobody can copy a webhook URL and impersonate a character.

Thanks for helping keep Tavern and its communities safe. 🛡️
