# UWI Team Survey System

Automated weekly/monthly team pulse survey built entirely on GitHub — no external services needed (except Gmail for email notifications).

## How it works

```
Every Monday 9am UTC
        ↓
GitHub Actions opens a GitHub Issue (survey form)
        ↓
Teammates get an email with a direct link
        ↓
Each teammate fills the issue checkboxes and closes it
        ↓
GitHub Actions collects the response → saves as JSON in /responses/
        ↓
Admin opens dashboard (GitHub Pages) → sees charts + table
```

---

## Setup (one-time, ~10 minutes)

### 1. Copy files into your repo

Place all these files into your GitHub repository:

```
.github/
  ISSUE_TEMPLATE/
    survey.yml
  workflows/
    survey-trigger.yml
    survey-collect.yml
survey-config.yml
dashboard/
  index.html
responses/          ← auto-created by the bot
```

### 2. Edit `survey-config.yml`

- Add your team's GitHub usernames to the `team` list
- Set `schedule` cron (weekly or monthly)
- Optionally customise the questions

### 3. Create the issue label

In your GitHub repo: **Issues → Labels → New label**
- Name: `team-survey`
- Color: any (suggested: `#EEEDFE`)

### 4. Set up GitHub Secrets

Go to: **Settings → Secrets and variables → Actions → New repository secret**

| Secret name | Value |
|---|---|
| `MAIL_USERNAME` | Your Gmail address (e.g. `uwi-surveys@gmail.com`) |
| `MAIL_PASSWORD` | A Gmail **App Password** (not your account password — [generate here](https://myaccount.google.com/apppasswords)) |
| `SURVEY_RECIPIENTS` | Comma-separated emails of all teammates |

### 5. Enable GitHub Pages for the dashboard

**Settings → Pages → Source: Deploy from branch → Branch: main → Folder: /dashboard**

Your dashboard will be at: `https://{org}.github.io/{repo}/`

### 6. Configure the dashboard

Edit `dashboard/index.html`, lines 230–231:
```js
const GITHUB_OWNER = "your-org-or-username";
const GITHUB_REPO  = "your-repo-name";
```

### 7. Test it

Run the trigger manually: **Actions tab → "Open Weekly Survey" → Run workflow**

---

## Changing the schedule

Edit `.github/workflows/survey-trigger.yml`, the `cron` line:

| Frequency | Cron |
|---|---|
| Every Monday 9am UTC | `0 9 * * 1` |
| Every 2 weeks (1st & 15th) | `0 9 1,15 * *` |
| Monthly (1st of month) | `0 9 1 * *` |

Then also update `survey-config.yml` `schedule:` field (for documentation).

---

## Adding or changing questions

Edit `survey-config.yml` — the questions list.  
Then update `.github/ISSUE_TEMPLATE/survey.yml` to match.  
The parser in `survey-collect.yml` looks for section headings that match the `label:` field — keep them consistent.

---

## File structure after first responses

```
responses/
  2025-W04/
    alice.json
    bob.json
    carol.json
  2025-W05/
    alice.json
    dave.json
```

---

## Troubleshooting

**Survey issue not created:** Check Actions tab for errors. Most common: MAIL_* secrets missing.

**Response not collected:** The workflow triggers on issue `closed`. Make sure the issue has the `team-survey` label.

**Dashboard shows demo data:** Set `GITHUB_OWNER` and `GITHUB_REPO` correctly in `dashboard/index.html`.

**Email not sent:** Make sure `MAIL_PASSWORD` is a Gmail App Password, not your regular password. 2FA must be on for App Passwords to work.
