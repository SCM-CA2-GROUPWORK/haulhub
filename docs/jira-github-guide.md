# Jira ↔ GitHub Linking Guide

| Field | Detail |
| :--- | :--- |
| **Parent Plan** | `SCMP-HH-001` (v1.0) — Software Configuration Management Plan |
| **Related** | [`CONTRIBUTING.md`](../CONTRIBUTING.md) — branch, commit and PR conventions |
| **Version** | 1.0 |
| **Task Reference** | `H2-12` |
| **Jira Project** | `Haulhub_2` (key: `H2`) |
| **GitHub Organization** | `SCM-CA2-GROUPWORK` |

This guide explains how GitHub work gets linked to Jira tickets, and what you need to do so your branches, commits and pull requests (PRs) show up against the right ticket.

`SCMP-HH-001` requires every change to a controlled item to be traceable to its Change Request (§3.3, §6.4). The Jira ↔ GitHub link creates that trail automatically, as long as you put the Jira key in the right places.

---

## Contents

1. [How It Works](#1-how-it-works)
2. [One-Time Setup](#2-one-time-setup)
3. [Linking Your Work](#3-linking-your-work)
4. [Smart Commits](#4-smart-commits)
5. [Where to See the Links](#5-where-to-see-the-links)
6. [End-to-End Example](#6-end-to-end-example)
7. [Troubleshooting](#7-troubleshooting)
8. [Quick Reference](#8-quick-reference)

---

## 1. How It Works

Jira and GitHub are connected through the **GitHub for Jira** app, which a Jira admin installs and links to our GitHub organization. Once it's connected:

- GitHub sends Jira an update every time someone pushes a branch or commit, or opens, updates or merges a PR.
- Jira scans that activity for **issue keys**, such as `H2-15`.
- Any branch, commit or PR containing a valid key is attached to that Jira ticket.

There is no manual linking step. **The issue key is the link.** If the key is missing or written wrongly, the work won't appear in Jira.

```text
 You push / open PR        GitHub for Jira app          Jira ticket H2-15
 ───────────────────  ──▶  finds "H2-15" in branch,  ──▶  Development panel shows:
 feature/H2-15-...         commit message or PR title     branches · commits · PRs · builds
```

---

## 2. One-Time Setup

### 2.1 Admins (done once per organization)

These steps are done by the Jira/GitHub admins (SCM Manager / DevOps), not by every collaborator:

1. In Jira, install **GitHub for Jira** from the Atlassian Marketplace.
2. Connect it to the `SCM-CA2-GROUPWORK` GitHub organization and grant access to the HaulHub repository.
3. Enable **Smart Commits** for the connection (see §4).
4. *(Optional)* Add a GitHub **autolink reference** under *Repository → Settings → Autolink references*:
   - Prefix: `H2-`
   - Target URL: `https://<your-site>.atlassian.net/browse/H2-<num>`

   This turns every `H2-XX` mentioned in GitHub into a clickable link back to Jira.

When a repository is first connected, past activity can take a while to import. New activity normally appears within a few minutes.

### 2.2 Collaborators (each person, once)

1. **Have a Jira account** on the `Haulhub_2` project, and make sure you can see the tickets you're assigned.
2. **Match your Git email to your Jira email.** Smart Commits only work when the commit author email matches a Jira user. Check and set it with:
   ```bash
   git config user.email                      # check current value
   git config user.email "you@example.com"    # set it for this repo
   ```
   If you keep your GitHub email private, use the same email in Jira, or ask an admin to add your commit email to your Jira profile.
3. **Make sure you're a member of the GitHub organization** with access to the HaulHub repository.

---

## 3. Linking Your Work

Put the Jira issue key in **every** place below. These are the same rules as `CONTRIBUTING.md`, so if you follow that guide, linking happens automatically.

| Where | Format | Example |
| :--- | :--- | :--- |
| **Branch name** | `<type>/<JIRA-ID>-<short-description>` | `feature/H2-15-add-docs-faq-md` |
| **Commit message** | `<type>(<JIRA-ID>): <subject>` | `docs(H2-15): add user FAQ for senders and drivers` |
| **PR title** | Same as the commit format | `docs(H2-15): add user FAQ for senders and drivers` |
| **PR description** | `## Jira` section with the key and link | `H2-15 — https://<site>.atlassian.net/browse/H2-15` |

### Rules for issue keys

- **Uppercase only.** Write `H2-15`, not `h2-15` or `H2_15`. Jira may not recognize lowercase or underscore versions.
- **Exact key.** The ticket must exist in the `H2` project. A typo such as `H2-150` instead of `H2-15` links your work to the wrong ticket, or to none.
- **One ticket per branch and PR.** You *can* mention several keys, but `CONTRIBUTING.md` §5.4 asks for one Jira ticket per PR. Extra keys also clutter other tickets' history.
- **Keys must be separated from surrounding text** by spaces, `/`, `-`, `(` or `)`. `feature/H2-15-...` and `(H2-15)` both work. `xH2-15` does not.

### Creating a branch from Jira

You can also start from the ticket itself. On a Jira ticket, open the **Development** panel and choose **Create branch**. This opens GitHub with the key already filled in.

Before creating the branch, check that the suggested name still follows `CONTRIBUTING.md` §3:
- the right type prefix, such as `feature/`
- a lowercase description

---

## 4. Smart Commits

Smart Commits let you act on a Jira ticket from a commit message: add a comment, log time, or move the ticket to a new status.

### Syntax

```text
<JIRA-ID> #<command> <value>
```

| Command | What it does | Example |
| :--- | :--- | :--- |
| `#comment` | Adds a comment to the ticket | `H2-15 #comment FAQ draft ready for review` |
| `#time` | Logs work time | `H2-15 #time 1h 30m` |
| `#<transition>` | Moves the ticket to a workflow status | `H2-15 #in-review` |

For a transition, use your board's status name in lowercase, with spaces replaced by hyphens. For example, "In Review" becomes `#in-review`. Check the exact status names on the `Haulhub_2` board.

### HaulHub rules for Smart Commits

- **Keep the subject line in `CONTRIBUTING.md` format.** Put Smart Commit commands in the commit **body**, not the subject:
  ```text
  docs(H2-15): add user FAQ for senders and drivers

  H2-15 #comment Added 8 sections with TBC markers for open policy decisions
  H2-15 #time 2h
  ```
- **Don't use transitions in squash-merge messages.** A squashed commit can repeat commands from the original branch commits, which can move a ticket twice or log time twice. Make status changes on the Jira board, or let them happen on the PR (§5).
- **Don't use transitions to skip review.** Moving a ticket to *Done* doesn't replace PR approval, CCB approval or passing CI checks.
- Smart Commits silently do nothing if your Git email doesn't match a Jira user (§2.2), or if the transition name doesn't exist.

---

## 5. Where to See the Links

### In Jira

Open any ticket and look at the **Development** panel on the right. It shows counts and links for:

- **Branches** whose names contain the key
- **Commits** whose messages contain the key
- **Pull requests**, with their status: *Open*, *Merged* or *Declined*
- **Builds** and **deployments** from GitHub Actions, if workflows are connected

Click a count to see the details and jump straight to GitHub.

### On the Jira board

Ticket cards can show a development icon once they have linked work. Use this to spot tickets that are *In Progress* but have no branch yet, or tickets that are *Done* but still have open PRs.

### In GitHub

With the autolink set up (§2.1, step 4), every `H2-XX` in a commit, PR title, PR description or comment becomes a link to the Jira ticket.

---

## 6. End-to-End Example

Ticket **H2-15 — Add docs/faq.md** is assigned to you.

```bash
# 1. Start from up-to-date main
git checkout main && git pull origin main

# 2. Create a branch with the key in its name   → branch appears on H2-15
git checkout -b feature/H2-15-add-docs-faq-md

# 3. Commit with the key in the subject         → commit appears on H2-15
git add docs/faq.md
git commit -m "docs(H2-15): add user FAQ for senders and drivers" \
           -m "H2-15 #comment First draft of FAQ pushed for review"

# 4. Push                                       → comment is added to H2-15
git push -u origin feature/H2-15-add-docs-faq-md
```

5. Open a PR titled `docs(H2-15): add user FAQ for senders and drivers`, with the Jira link in the `## Jira` section. **The PR appears on H2-15 as *Open*.**
6. After approval and merge, **the PR shows as *Merged* on H2-15.** Move the ticket to *Done*, or let your board's automation do it.

---

## 7. Troubleshooting

| Problem | Likely Cause | Fix |
| :--- | :--- | :--- |
| Branch/commit/PR not showing on the ticket | Key missing, lowercase or mistyped | Check that `H2-XX` is uppercase and correct. For a branch, rename it. For an unpushed commit, use `git commit --amend`. For a PR, edit the title. |
| Nothing shows for any ticket | Repo not connected, or sync delayed | Wait a few minutes, then ask an admin to check the GitHub for Jira connection |
| Smart Commit did nothing | Git email doesn't match Jira, or wrong command/transition name | Check `git config user.email` and the exact status names on the board |
| Work appears on the wrong ticket | Typo in the key | Fix the PR title/description. For pushed commits on `main`, don't rewrite history (`CONTRIBUTING.md` §7). Add a Jira comment pointing to the correct ticket. |
| Ticket moved status unexpectedly | Transition command repeated in a squash commit | Avoid transitions in squash messages (§4). Move the ticket back manually. |
| Already-pushed commit has no key | Message written before following conventions | On your own unmerged branch: `git rebase -i` to reword, then `git push --force-with-lease`. Never force-push `main` or `release/*`. |

---

## 8. Quick Reference

```text
Key format:    H2-<number>        (uppercase, exact, must exist)

Put it in:     branch   feature/H2-XX-short-description
               commit   <type>(H2-XX): <subject>
               PR       title in commit format + "## Jira" section with link

Smart Commit:  H2-XX #comment <text>
               H2-XX #time 1h 30m
               H2-XX #<status-name>      ← not in squash-merge messages
               (put these in the commit body; Git email must match Jira)

See links:     Jira ticket → Development panel
```

Problems with the connection itself (missing repos, sync failures, permissions) go to the SCM Manager / DevOps admin.
