# UWI Team Survey System

Automated weekly/monthly team pulse survey built entirely on GitHub — no external services needed.

## How it works

```
Every Monday 9am UTC
        ↓
GitHub Actions opens a GitHub Issue (survey form)
        ↓
Each assignee gets an individual email on their Accenture Outlook
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

```yaml
team:
  - "acn-ashutoshkumar"
  - "acn-urvashiisharma"
  - "Prasath-7"
```

### 3. Create the issue label

In your GitHub repo: **Issues → Labels → New label**
- Name: `team-survey`
- Color: `#534AB7`

### 4. Teammates — verify your GitHub email

No secrets or passwords needed. GitHub automatically emails each assignee when a survey issue is created.

Each teammate must verify their Accenture email is set as primary:

**GitHub → Settings → Emails → Primary email**

### 5. Configure the dashboard

Edit `dashboard/index.html`, lines 230–231:

```js
const GITHUB_OWNER = "acn-ashutoshkumar";
const GITHUB_REPO  = "uwi-team-survey";
```

### 6. Enable GitHub Pages for the dashboard

**Settings → Pages → Source: Deploy from branch → Branch: master → Folder: /dashboard**

Your dashboard will be at:
```
https://acn-ashutoshkumar.github.io/uwi-team-survey/
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

Then also update `survey-config.yml` `schedule:` field.

---

## Adding or changing questions

Edit `survey-config.yml` — the questions list.
Then update `.github/ISSUE_TEMPLATE/survey.yml` to match.
The parser in `survey-collect.yml` looks for section headings that match the `label:` field — keep them consistent.

---

## File structure after first responses

```
responses/
  2026-W30/
    acn-ashutoshkumar.json
    acn-urvashiisharma.json
    Prasath-7.json
```

---

## Troubleshooting

**Survey issue not created:** Check Actions tab for errors. Make sure `team-survey` label exists in the repo.

**Response not collected:** The workflow triggers on issue `closed`. Make sure the issue has the `team-survey` label.

**Email not received:** Teammate should check their GitHub primary email — **GitHub → Settings → Emails → Primary email** — and make sure it is their Accenture email.

**Dashboard shows demo data:** Set `GITHUB_OWNER` and `GITHUB_REPO` correctly in `dashboard/index.html` and make sure GitHub Pages is enabled.
