# Yaqiz user guide

A walkthrough of Yaqiz for the people who use it every day: security analysts, asset owners, team leads and
administrators. It follows the path a finding takes, from the scanner report to the fix, and shows each screen you
use along the way.

Written for **Yaqiz 1.1.9**. Installing, upgrading and operating the server (TLS, directory sign-in, single sign-on,
email, backups) is covered by the installation guide, `docs/on-prem-installation.md`, and the command-line reference,
`docs/cli-reference.md`, both in the release package, and summarized in the [main README](../../README.md).

![The Yaqiz Command Center](images/01-home.png)

## How Yaqiz works

```
  Scanners and reports            Yaqiz                                             Your teams
 ──────────────────────  ┌──────────────────────────────────────────────────┐  ─────────────────────
  Nessus, ZAP, Trivy,  ──▶  Import      deduplicate, enrich (EPSS, KEV),   ──▶  My Work, findings lists
  Semgrep, … 98 types    │  Connectors  prioritize by business context,     │   Jira and other trackers
  Excel / Word / CSV   ──▶  CI/CD API   set SLA due dates                  ──▶  email, Slack, Teams
  pentest reports        │              Triage Engine rules act on them     │   dashboards and reports
  advisory feeds       ──▶  Advisories  match new CVEs to your findings    ──▶  alerts on new CVEs
                         └──────────────────────────────────────────────────┘
```

1. **Bring findings in** from scanner reports, spreadsheets and Word reports, API connectors and CI/CD pipelines.
2. Yaqiz **deduplicates** them, so a finding reported by several tools or scans is tracked once, and **enriches** them
   with exploit data (EPSS probability, CISA known exploited vulnerabilities).
3. Each finding gets a **priority** from its severity, exploitability and the business context of its asset, and an
   **SLA due date**.
4. Analysts **triage** the findings: confirm, assign, accept the risk or mark false positives. **Rules** in the Triage
   Engine do the routine part automatically: assign owners, open tickets, notify people.
5. **Dashboards, insights and scheduled reports** show the posture, the trends and who needs to act.
6. **Advisory feeds** (CISA KEV, NVD, the GitHub Advisory Database and the RSS feeds you turn on or add) bring in new
   CVEs and zero-days. The **Advisories** page shows which of your open findings and assets each one affects and which
   match your watchlist, and alerts tell you about new ones that concern you.

## Chapters

| Chapter | What you learn | For |
|---|---|---|
| [1. First steps](01-first-steps.md) | Sign in, two-step verification, signing out after inactivity, the layout, Home and My Work, why menus differ between users | Everyone |
| [2. Inventory](02-inventory.md) | Organizations, assets, the hierarchy and change plans, engagements and tests, members and roles, assigning an owner to many assets at once | Everyone; asset owners |
| [3. Bringing in findings](03-bringing-in-findings.md) | Importing scanner reports, one upload for many assets, Excel / Word / CSV findings, the Universal Parser, the 26 connectors (including CISO Assistant), CI/CD | Analysts, DevOps |
| [4. Working with findings](04-working-with-findings.md) | Finding lists and filters (category, end of life, age, owner), the finding page, statuses, risk acceptances, groups, the Vulnerability Explorer, Advisories | Analysts, asset owners |
| [5. Automation and integrations](05-automation.md) | Triage Engine rules, runs and deliveries, webhook receivers, Jira, ServiceDesk Plus and other issue trackers, Slack and Teams, advisory alerts | Analysts, team leads |
| [6. Dashboards and reports](06-dashboards-and-reports.md) | Home and the posture score, dashboards, insights with their filters (owner, category, age) and charts, reports and schedules, the calendar | Team leads, managers |
| [7. Administration](07-administration.md) | Users, groups and roles, sign-in and sign-out after inactivity, resetting two-step verification, SLAs and prioritization, advisory feeds, notifications, license, System Status, audit log, command-line help | Administrators |

New to Yaqiz? Read chapters 1 to 4 in order: after them you can import your first report and work through its
findings. Administrators setting up a new installation start with [chapter 7](07-administration.md), section
"Your first week".

## Conventions

* Menu paths use **›**: **Connect › Import › Add Findings** means the **Import** entry in the **Connect** section of the
  sidebar, then **Add Findings**. Button and field names are in **bold**, exactly as on screen.
* What you see depends on your role and on the modules your administrator enabled. When a page or button described
  here is missing, check **Profile › Your permissions** or ask your administrator (chapter 1 explains why).
* The screenshots were taken on a test installation filled with fictional sample data: the organizations, people,
  assets and findings in them are made up. Your own installation starts empty and shows only your organization's
  data (the **Advisories** page also lists public advisories from the feeds, once the server can reach them).
