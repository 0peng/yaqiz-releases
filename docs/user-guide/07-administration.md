# 7. Administration

This chapter is for administrators. It starts with what to set up in the first week, then describes each page under
**Settings**, **Risk & Matching**, **Data Model**, **Notifications** and **Audit Logs**. Server tasks such as TLS, the
mail server (SMTP), the LDAP server, backups and upgrades are covered in the installation guide
(`docs/on-prem-installation.md` in the release package).

In this chapter:

* [Your first week](#your-first-week)
* [Users, groups and roles](#users-groups-and-roles)
* [Sign-in methods](#sign-in-methods)
* [System Settings](#system-settings)
* [Feature Flags](#feature-flags)
* [License](#license)
* [System Status](#system-status)
* [Risk & Matching](#risk--matching)
* [Data Model](#data-model)
* [Notifications](#notifications)
* [Audit Logs](#audit-logs)

The **Settings** part of the menu appears only for superusers and for users who hold at least one configuration
permission. Users who may configure assets (`asset.configure`) but hold no configuration permission see only
**Settings › Risk & Matching › Dedupe Pools**.

## Your first week

1. **Settings › System Status**: fix every red and amber check, then send a test email.
2. **Settings › License**: install the license file from your vendor (superusers only).
3. **Settings › System Settings**: set the **Team name**; under **Notifications** turn on **Email** and set the
   recipients and the Slack or Teams URLs; under **Users** choose the **Default group** and decide on **Require
   two-step verification**; add a **Login banner** if your policy needs one. Click **Save settings**.
4. **Settings › Roles** and **Settings › Groups**: create one group per team and decide which roles they get.
5. **Settings › Users**: create accounts, or let LDAP and single sign-on users create theirs at first sign-in. Create a
   service user for each CI/CD pipeline.
6. Grant access where people work: on an organization or asset, open the **Members** tab and add users or groups.
7. **Settings › Risk & Matching**: choose the default **Service Level Agreements** configuration and
   **Prioritization Engine**, and check that **Finding Enrichment** loads EPSS and KEV data.
8. **Settings › Notifications › System**: choose which events go to Slack, Teams, email and webhooks. Users set their
   own alerts under **Settings › Notifications › Personal**.
9. **Settings › Feature Flags**: switch on or off the optional modules.

## Users, groups and roles

A **role** is a set of permissions. You grant a role in three ways:

* as the **Global role** of a user: it applies to every organization and asset;
* as the **Global role** of a group: every member gets it everywhere;
* as a **membership** on one organization or asset: open the organization or asset, go to the **Members** tab and
  click **Add member**. Organization roles are inherited by the organization's assets.

| Built-in role | What it allows |
|---|---|
| **Reader** | View everything in scope. |
| **API_Importer** | View, plus import and reimport scans (for CI/CD service users). |
| **Writer** | View, import, triage and edit findings, engagements and tests; generate reports and export. |
| **Maintainer** | Writer, plus edit assets, delete objects and manage members. As a global role, also creates organizations. |
| **Owner** | Full control, including deleting assets and managing owners. As a global role, also creates organizations. |

**Configuration permissions** control the administration pages. They apply only through a global role, and only
superusers can grant them.

| Permission | Code | Pages |
|---|---|---|
| Manage users, groups, roles | `config.users`, `config.groups`, `config.roles` | Settings › Users, Groups, Roles |
| Edit system settings | `config.settings` | System Settings, System Status, Matching Configuration, Dedupe Pools, Mitigation Policies, Finding Enrichment sources, Custom Fields, Asset Attributes, Regulations, Note Types |
| Manage SLA configurations | `config.sla` | Service Level Agreements |
| Manage prioritization engines | `config.engines` | Prioritization Engines |
| Manage test types, environments | `config.test_types`, `config.environments` | Test Types, Environments |
| Manage upstream and downstream connectors | `config.connectors` | Connect › Upstream, Downstream |
| Manage triage rules | `config.rules` | Act › Triage Engine |
| Manage system notifications | `config.notifications` | Notifications › System, Webhooks |
| Manage universal parsers | `config.parsers` | Connect › Import › Universal Parser |
| View audit logs | `config.audit` | Audit Logs |
| Manage finding and report templates | `config.templates` | Finding templates, report templates |

**Custom roles.** Built-in roles are read-only. On **Settings › Roles**, select a role and click **Clone**, or click
**New custom role** and choose **Start from**. In **Object permissions**, click a cell to toggle view, add, edit or
delete for an object; click a row or column header to toggle all of it. Add **Special permissions** such as **Approve
or reject risk acceptances** or **Generate reports**, then click **Save**. **Owner role** marks roles whose holders can
manage owners (superusers only). A role that is still assigned cannot be deleted.

![Roles page with a custom role selected](images/07-roles.png)

**Users.** On **Settings › Users**, click **New user** and fill in **Username**, **Email**, the names, an optional
**Password** and the **Global role**. **Active**, **Superuser** and **Service user** are checkboxes. Click **Create
user**. Only superusers can grant superuser status, and other administrators can only assign roles whose
permissions they hold themselves. A user's page shows **Effective permissions**, including access through groups and
memberships, and offers **Reset MFA**, **Deactivate** and **Delete**. Prefer **Deactivate**: it keeps the user's
history attributable.

**Groups.** On **Settings › Groups**, click **New group**, enter a **Name** and optionally a **Global role**, and click
**Create group**. On the group's page, choose a **User** and a **Role in group** and click **Add member**.

**Service users and API tokens.** A service user (checkbox **Service user**) is meant for CI/CD. It cannot sign in to
the web interface and does not need two-step verification. Create it with a password and give it the **API_Importer**
role on the organizations it imports into. The pipeline gets its token from `POST /api/v2/api-token-auth/` with the
user name and password, and sends it as `Authorization: Token <token>`. People find their own token in the user menu
under **API token**, where they can **Reveal**, copy or **Rotate** it. Rotating stops the old token at once.

## Sign-in methods

LDAP / Active Directory and single sign-on (OpenID Connect) are configured on the server; see section 4a of the
installation guide. In the web interface you then see:

* With LDAP on, the sign-in page asks for the directory account. With single sign-on on, it shows a **Sign in with
  …** button.
* Directory and single sign-on accounts are created at their first sign-in, without permissions. They join the
  **Default group** with its role, if you set one under **System Settings** (section **Users**). Otherwise, grant
  roles by hand.
* The user's page shows the **Sign-in** source: **Local password**, **Directory (LDAP)** or **Single sign-on**.
* **Require two-step verification** (section **Users**) makes local and LDAP users enroll an authenticator app at
  their next sign-in. Single sign-on users rely on the identity provider's MFA.
* Once everyone uses single sign-on or LDAP, turn off **Allow password sign-in**. Superusers can always sign in with a
  password as a break-glass path.

## System Settings

**Settings › System Settings** holds instance-wide behavior in sections; use **Filter sections** to find one. Change
values, then click **Save settings**, or **Discard**. All sections need **Edit system settings**; the
**Notifications** section can also be changed with **Manage system notifications**.

| Section | Key options |
|---|---|
| **General** | **Team name**, **Read-only mode** (superusers only: blocks changes by everyone else) |
| **Deduplication** | **Deduplicate findings**, **Delete excess duplicates**, **Maximum duplicates**, **False positive history** |
| **SLA & grading** | **Finding SLAs**, **Notify on SLA breach**, **Asset grading**, grade thresholds A to D |
| **Risk acceptance** | **Default expiration**, **Notify before expiration**, **Block self-approval** |
| **Engagements** | **Auto-close engagements** and its **Grace period** |
| **Imports** | Background imports from the UI and the API, **Background threshold** |
| **Notifications** | **Email** and **Recipients for system emails**, **Slack** and its URL, **Microsoft Teams** and its URL, **Webhooks** and **Webhook timeout** |
| **Enrichment** | **EPSS scores**, **CISA KEV catalog** and their feed URLs |
| **Banners** | **Announcement** with style, **Login banner**, **Report disclaimer** |
| **Users** | **Default group**, **Role in the default group**, **Minimum password length**, **Require two-step verification**, **Allow password sign-in** |

![System Settings, Notifications section](images/07-system-settings.png)

## Feature Flags

**Settings › Feature Flags** turns optional modules on or off for every user at once. Only superusers can change
them. In 1.1.5 these flags have a visible effect:

* **Locations**, **Upstream Connectors**, **Triage Engine** and **Root Cause Correlation** show or hide the menu entries
  **Inventory › Locations**, **Connect › Upstream**, **Act › Triage Engine** and **Act › Root Causes**.
* **Risk Acceptance Approvals** (off by default, Beta) makes new risk acceptances wait for approval.

The page also lists other modules, for example **Custom Fields**, **Dedupe Pools** and **AI Assistant**.

## License

**Settings › License** shows who the installation is licensed to, the **Edition**, the expiry date, the **Grace period
after expiry** and the **Mode**, plus usage of **Assets** and **Users (active, excluding service users)** against the
license limits. To install or renew, a superuser pastes the license text or clicks **Choose file**, then **Install**.
A banner warns before expiry. After expiry and the grace period (14 days unless the license says otherwise), Yaqiz is
read-only: viewing, exporting and installing a license still work, and nothing is deleted. Limits only block creating
more assets or users than licensed.

## System Status

**Settings › System Status** answers one question: is this installation configured and healthy? Superusers and
holders of **Edit system settings** can open it. Each check shows a green (OK), grey (information), amber (warning) or
red (problem) icon, a one-line summary and, for warnings and problems, **How to fix**. Click **Check again** after a
fix. Most fixes happen on the server; the installation guide explains them.

| Check | What it tells you |
|---|---|
| **Background jobs** | The worker and scheduler run. If not, background imports, rules, emails, syncs, reports and SLA checks stop. |
| **Database** | The database is reachable, its version and size. |
| **Disk space** | Free space on the data disk. Amber below 15% or 10 GB, red below 5% or 2 GB. |
| **Backups** | When the last backup ran. Amber after 36 hours, red after 7 days. |
| **Stored credentials** | Tracker tokens, webhook secrets and similar are encrypted with a dedicated data key. |
| **Email** | A mail server is configured, the sender address is real, and the last rule email did not fail. Amber when email is not configured but notifications or rules use it. |
| **TLS certificate** | Validity, expiry within 30 days, host name match, self-signed. |
| **License** | Valid, in trial, expiring, in grace or expired. |
| **Vulnerability feeds (EPSS, KEV)** | When the feeds last loaded. Amber after 3 days without an update. |
| **Sign-in** | Which sign-in methods are active, and whether LDAP or single sign-on settings are incomplete. |
| **Site address and administrator email** | Links in emails work, and superusers have a real email address. |
| **Time zone and clock** | The server time zone and time. |

**Send a test email** sends a message to the address you enter (your own by default) and shows the mail server's
answer: accepted, or the reason it was refused. The button is off while email is not configured. You can send up to
10 test emails per hour.

**The admin banner.** When a check is amber or red, superusers and holders of **Edit system settings** see a colored
bar on every page: "This installation needs attention: …". It is red for problems and amber for warnings. Click
**View system status**, or **Dismiss** to hide it until the counts change.

![System Status with checks and the test email card](images/07-system-status.png)

## Risk & Matching

These pages are under **Settings › Risk & Matching**.

* **Service Level Agreements** (`config.sla`): remediation deadlines. Yaqiz ships **Default**, **PCI DSS 6.3.3**,
  **FedRAMP Rev 5**, **FedRAMP VDR**, **Risk based** and **No SLA Enforced**. Click **New SLA configuration**, choose
  the **Basis** (severity or risk band), the days per severity or band and **SLA starts on** (**Detection date**,
  **Vulnerability publish date**, **Fix available date** or **Earliest known date**). Options: **Default
  configuration**, **Restart on reactivation**, **Cap by KEV due date** and **VDR tiering**. Saving recomputes the
  due dates. Assign a configuration to an asset in the asset's **SLA configuration** field (needs `asset.configure`).
* **Prioritization Engines** (`config.engines`): how severity, exploitability, exposure and business context become
  a priority. Yaqiz ships **Default** and **Exploit-driven**. **Multipliers** run from 0 (ignored) through 1.0 (default)
  to 2. **Risk bands** set the score thresholds (0 to 100) between Low, Medium, Needs Action and Urgent. **Actively
  exploited floor**, **Reachable floor** and **Unreachable ceiling** bound the band. Use **Try it** to score a
  hypothetical finding, **Save**, then **Recalculate** to rescore the findings of the engine's assets.
* **Matching Configuration** (`config.settings`): per tool, how findings count as the same: **Same tool**, **Cross
  tool** and **Reimport**. Set the **Algorithm** and the **Hash code fields**; **Rehash existing findings** applies new
  fields to old findings. **Changed from defaults only** shows your customizations; **Reset to default** undoes one.
* **Dedupe Pools**: groups of assets whose findings deduplicate against each other (**Kind**: **Same tool** or **Cross
  tool**). Adding or removing an asset needs `asset.configure` on it.
* **Mitigation Policies** (`config.settings`): standard remediation guidance with a **Title**, the **Policy** text and
  an optional **Default policy**. Attach a policy to findings by hand or with a rule's **Set Mitigation Policy** node.
* **Finding Enrichment**: shows **CVEs tracked**, **Known exploited (KEV)** and **Last enriched**. Under **Sources**,
  turn **EPSS lookup** and **CISA KEV lookup** on or off and optionally set an internal mirror in **EPSS feed URL**
  and **KEV feed URL**; an empty URL uses the public feed. Enrichment runs daily; superusers can click **Run
  enrichment now**. Without internet access, a server administrator loads feed files on the server (installation
  guide, section 4). KEV status raises risk through each engine's **Actively exploited floor** and can cap SLAs.

![Prioritization engine with multipliers, risk bands and Try it](images/07-prioritization-engine.png)

## Data Model

These pages are under **Settings › Data Model**.

| Page | Purpose and fields |
|---|---|
| **Custom Fields** | Typed fields on **Assets**, **Findings** or **Engagements**: **Type** (Text, Integer, Decimal, Boolean, Date, Single select, Multi select, URL), **Label**, **Key**, **Options**, **Help text**, **Order**, **Required**. |
| **Test Types** | The scanners and assessment types that imports record. **Category**, **Active**, **Static tool**, **Dynamic tool**. Needs `config.test_types`. |
| **Environments** | Where tested code runs, with a **Production mapping**. Needs `config.environments`. |
| **Asset Attributes** | Option lists for an asset's platform, lifecycle and origin: **Label**, **Value**, **Icon**, **Order**. |
| **Regulations** | Compliance frameworks for assets (PCI DSS, GDPR, ISO 27001, NIS2 and others are built in). |
| **Note Types** | Categories for finding notes. **Single** allows one note of the type per finding; **Mandatory** requires one. |

Pages without another permission named here need **Edit system settings**.

## Notifications

These pages are under **Settings › Notifications**.

* **Personal** (**My notifications**): every user chooses which events reach them as an **In-app alert** (the bell)
  and by **Email**, then clicks **Save**. Email needs an address in the user's profile and email enabled under
  **System Settings**. Users without the **Settings** menu open the page with **Search** (Ctrl K), entry
  **Notifications › Personal**.
* **System**: routes events instance-wide to **Slack**, **Teams**, **Email** and **Webhooks**. Chips at the top show
  which channels are enabled; a disabled channel delivers nothing. Configure the channels under **System Settings**
  (section **Notifications**). Needs **Manage system notifications**.
* **Webhooks**: click **New webhook** and enter a **Name**, a **URL** and optionally **Auth header name** and **Auth
  header value** (write-only). Events arrive as JSON POST requests with an `X-Yaqiz-Event` header. Use **Send test
  event** in the row menu. After failed deliveries a webhook can become **Temporarily inactive**; when errors go on
  for more than a day it becomes **Inactive**. Fix the receiver, then click **Activate** in the row menu.

Events include asset added, scan imported, finding added, closed or reopened, SLA breached or approaching, risk
acceptance requested, approved or expiring, review requested, user mentioned, connector failure, rule run failed and
report generated.

## Audit Logs

**Settings › Audit Logs** records who changed what, and when; entries cannot be changed. The page needs **View audit
logs** (`config.audit`). Search by object or actor, and filter by **Action** (**Create**, **Update**, **Delete**,
**Login**, **Logout**, **Action**), **Type** and **Actor**. Click an entry to see its changes. Failed sign-ins are
recorded too.

Back to the [guide index](README.md)
