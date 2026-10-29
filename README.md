# Rapid POS Barton MCP AI Connector

**Updated October 29th 2026**

The Rapid Barton MCP AI Connector gives Claude live, read-only access to your CounterPoint data. Ask questions in plain English, like "What are my sales today versus the same day last year?", or build your own dashboards, reports, and tools on top of CounterPoint. Barton is a custom-built MCP (Model Context Protocol) server with retail and CounterPoint skills built in. Along with the data, it gives the AI the context it needs to read that data correctly.

> **Note:** Barton is read-only. It can't add, change, or delete anything in CounterPoint.

> **New to Barton? Start here.** A full set of FAQs is in Section 17 at the end of this document. If you only read one part, make it [The Basics](#the-basics). It covers what Barton is, what it can see, where you use it, and who it's for, all in a few minutes.

## Overview

Barton runs on your CounterPoint server and connects Claude to your CounterPoint database through the Rapid CP API. Claude is the "brain" that understands your question and explains the answer. Barton is the "brawn" that runs the queries safely and supplies the CounterPoint knowledge Claude needs, including the database structure, the CounterPoint Data Dictionary, documentation, and Rapid's own CounterPoint expertise. Barton ships with the **Counterpoint AI Guide**, a set of prebuilt dashboards and questions tested to match CounterPoint's own reports. Every query runs under built-in guardrails: it's read-only, written to avoid database lockups, and capped on how many rows it returns. You use Barton from Claude's website or desktop app, on a desk, phone, or tablet. It doesn't add anything to the CounterPoint menus. Rapid deploys updates automatically to subscribers.

### Minimum System Requirements

- Minimum Counterpoint version: 8.5.6.2
- Minimum SQL Server version: 2016
- Minimum Supported Operating System version: Windows Server 2016 or Windows 11 Pro
- Minimum PowerShell version: 5.1
- SQL Server set to Mixed Mode authentication, with a SQL administrator login available during installation
- Outbound internet (HTTPS) access from the CounterPoint server. No inbound firewall changes are needed.

Rapid installs and configures Barton for you.

> **Warning**
>
> Your environment must meet our CI/CD Connector Requirements (server access, firewall rules, etc.) before any install or upgrade. Troubleshooting, manual installs, or follow-up work resulting from unmet requirements will be billed at standard T&M rates.
>
> If your system does not meet these minimum requirements, please consult your Care Team Lead (vCIO) for an upgrade quote.

Each user also needs their own Claude account that supports custom connectors (Free, Pro, Max, Team, or Enterprise). The Free plan allows 1 custom connector, which is enough for Barton, but has tight usage limits. On Team and Enterprise plans, an account owner adds Barton once and each user then connects to it.

## Table of Contents

- [Section 1: What Barton Is](#section-1-what-barton-is)
- [Section 2: Data Access and Security](#section-2-data-access-and-security)
- [Section 3: Barton Guardrails](#section-3-barton-guardrails)
- [Section 4: Adding Users](#section-4-adding-users)
- [Section 5: Connecting Barton to Claude](#section-5-connecting-barton-to-claude)
- [Section 6: The Counterpoint AI Guide](#section-6-the-counterpoint-ai-guide)
- [Section 7: Prebuilt Dashboards](#section-7-prebuilt-dashboards)
- [Section 8: Prebuilt Questions](#section-8-prebuilt-questions)
- [Section 9: Adding Custom Dashboards and Questions](#section-9-adding-custom-dashboards-and-questions)
- [Section 10: Asking Good Questions](#section-10-asking-good-questions)
- [Section 11: How to Read the Numbers](#section-11-how-to-read-the-numbers)
- [Section 12: Reports, Exports, and Scheduled Reporting](#section-12-reports-exports-and-scheduled-reporting)
- [Section 13: Writing Data Back to CounterPoint](#section-13-writing-data-back-to-counterpoint)
- [Section 14: Barton Tools for Developers](#section-14-barton-tools-for-developers)
- [Section 15: Troubleshooting](#section-15-troubleshooting)
- [Section 16: Pricing and Subscription](#section-16-pricing-and-subscription)
- [Section 17: Frequently Asked Questions](#section-17-frequently-asked-questions)
- [Conclusion](#conclusion)

---

## Section 1: What Barton Is

Barton is a custom MCP server built by Rapid for CounterPoint. It isn't AI. It doesn't think, guess, or make decisions. When Claude asks for something, Barton supplies its tools, resources, and prompts and runs the queries.

| Role | Who | What it does |
|---|---|---|
| Brain | Claude | Understands your question, decides what to look up, and explains or presents the answer |
| Brawn | Barton | Connects to CounterPoint, runs read-only queries, and supplies the retail and CounterPoint context needed to read the results |

```
Claude (brain)  →  Barton MCP server (brawn)  →  Rapid CP API  →  CounterPoint SQL database
```

### What Barton Can See

Barton has read access to your full CounterPoint SQL database. If it lives in the database, Barton can show it to Claude. Barton also gives Claude:

- The CounterPoint Data Dictionary (what each table and field means)
- CounterPoint documentation
- Rapid's own CounterPoint knowledge, such as how sales, taxes, returns, and inventory value should be calculated

Barton only works with CounterPoint. To combine CounterPoint data with another system, such as Klaviyo, Claude needs its own separate connection to that system.

### Supported AI Assistants

Claude (by Anthropic) is the only AI assistant Rapid builds for, tests, and supports. Other MCP-compatible assistants may work but are untested and not supported.

The standard Claude models (such as Sonnet) handle most questions well. Higher-reasoning models are available but use more of your plan.

### Ways to Use Barton

- **As an AI assistant.** Have simple conversations about your data, such as "What are my sales today versus the same day last year?"
- **As a builder's tool.** IT staff and developers can use Barton to build custom apps, dashboards, reports, and saved queries for CounterPoint.

### Common Uses

- Sales reporting by store, category, rep, or customer
- Finding which CounterPoint tables and fields hold the data you need
- Suggested min/max stocking levels, forecasting, and purchasing or transfer recommendations based on your sales history
- Marketing lists, such as customers who bought regularly and then stopped
- Recreating existing reports (for example, from a PDF) in PDF, Excel, or web formats

## Section 2: Data Access and Security

> **Important**
>
> Barton does not use CounterPoint User Security. Anyone you connect to Barton can see all sales, cost, profit, inventory, and customer data across every store, whatever their role in CounterPoint. **Treat Barton as an admin-only tool** and only connect people who are authorized to see everything. Limiting access by job function or user is possible as custom development.

### Personal Information Protection

Barton protects sensitive personal and account data:

- Birth dates, driver's license and tax IDs, card and bank numbers, and system credentials can't be returned at all.
- Customer addresses, email addresses, and phone numbers are limited to about 5 rows per answer. Ask for counts or totals instead of full lists.

### Trust, but Verify

Claude can make mistakes. Rapid has done everything in our power to steer Claude to the correct answer every time. The numbers Barton retrieves are accurate, but Claude's interpretation can be wrong, such as reading sales as gross instead of net, handling returns incorrectly, or misreading a time range. Always check the reasoning before acting on anything important. If Claude tells you to order 10,000 widgets, validate why before placing the order.

If an answer looks off, ask Claude to "show the query" or "explain how you calculated that." The SQL behind every answer is available to review.

### How Your Data Is Handled

- Barton works on a request-only basis. It fetches only the data a question needs.
- Your data stays in CounterPoint. Rapid does not store your data on its servers, and Barton sends nothing to Claude unless Claude asks for it.
- Claude's handling of data is governed by Anthropic's privacy policies.

## Section 3: Barton Guardrails

Barton has guardrails built in to protect your CounterPoint system.

| Guardrail | Behavior |
|---|---|
| Read-only | Barton can't add, change, or delete CounterPoint data. See Section 13 for how to get changes back into CounterPoint safely. |
| Safe queries | Queries are written to avoid locking up the database, and Barton blocks very large queries that could slow down your system. |
| 500-row limit | Each answer returns up to 500 rows (configurable). This doesn't limit what Barton can analyze. It can still total three years of sales by day. You just won't get more than 500 rows back in one answer. Barton can't export "all your data." |

If you need more than 500 rows, ask Claude for the query and steps to build a full export in Excel (see Section 12).

### Performance

Your server's resources are shared. Several users running big, complex questions at the same time can slow the server, the same way several large SQL reports running at once would. Keep an eye on heavy use, especially during business hours.

### What Barton Doesn't Do

- Change CounterPoint data
- Build Crystal Reports (see Section 12)
- Schedule or send reports on its own (see Section 12)
- Monitor Windows Server, operating system, or SQL Server health
- Connect to systems other than CounterPoint

## Section 4: Adding Users

Barton includes **unlimited users** with no per-user charge from Rapid. Each user's Claude subscription is separate.

A Claude account alone doesn't give access. Rapid creates each person's Barton sign-in, so contact Rapid Support to add someone later.

- During setup, send Rapid one email with the names and email addresses of all initial users. Setting everyone up at once is faster than adding people one at a time.
- Only include people authorized to see all CounterPoint cost, sales, and profit data (see Section 2).
- Each user should have their **own** Claude account. Don't share accounts. Claude personalizes answers based on past conversations, so a shared account mixes different people's history together.

## Section 5: Connecting Barton to Claude

After your deployment, Rapid sends a welcome email with your connector URL, a username, and a temporary password. You'll set a new password the first time you sign in.

### Before You Start

- Have your connector URL ready. It looks like this:

  ```
  https://<your-company>-cp-mcp.rapidextras.com/mcp
  ```

- **Leave any OAuth Client ID or Client Secret fields blank** when adding the connector. Your username and password go on the sign-in page that appears afterward. Anything typed in those fields causes a "Client not found" error.

### Individual Accounts (Free, Pro, Max)

1. Open Claude and go to **Settings > Connectors** (or **Customize > Connectors**, depending on your view).
2. Click the **+** button next to Connectors.
3. Select **Add custom connector**.
4. Paste in your connector URL.
5. Click **Add**, then follow the prompt to sign in.
6. To use it in a chat, click the **+** button in the message box, select **Connectors**, and turn on Barton for that conversation.

### Team and Enterprise Accounts

1. An account Owner or Primary Owner goes to **Organization Settings > Connectors**.
2. Click **Add**, select **Custom**, then **Web**.
3. Paste in your connector URL and save.
4. Each team member then goes to their own Connectors list, finds Barton (labeled **Custom**), and clicks **Connect** to sign in.
5. Turn it on per conversation with the **+** button > **Connectors**.

### Once You're Connected

Start a new chat and ask a question, or open the Counterpoint AI Guide. Your sign-in can time out after a while. If answers stop coming back, reconnect from your Connectors list.

## Section 6: The Counterpoint AI Guide

The Counterpoint AI Guide is the starting screen for Barton. It shows:

- **Sales today** and **Items below minimum stock** at the top
- **Dashboard cards** on the left (see Section 7)
- **Questions that lead to action**, grouped by category (see Section 8)

Tap a card or question to see the answer on the page. You can also type a card's number in chat ("run 1") or just ask in your own words.

- If you have more than one company, Claude labels each answer with its company.
- Stock figures use your **primary location**: your only location, or, if you have several, the one with the most sales in the last 90 days.
- Sales figures on the dashboards cover **all stores**. To see one store, ask for it by name.

## Section 7: Prebuilt Dashboards

| # | Dashboard | What it shows |
|---|---|---|
| 1 | Store manager dashboard | Today's sales against yesterday and last year, sales by rep, top customers, and alerts for low stock, open orders, and open layaways |
| 2 | Inventory health | Low stock, out of stock, on order, and dead stock at your primary location |
| 3 | Morning open | Yesterday's sales against the same weekday last week, open customer orders, and items below minimum |
| 4 | End-of-day summary | Today's totals against yesterday and last year, transaction counts, and items below minimum |
| 5 | Rep & customer performance | The last 7 days against the 7 before: sales, average ticket, top reps, and top customers |

## Section 8: Prebuilt Questions

The guide includes 25 prebuilt questions in 7 categories. Each one runs a tested query and shows the answer on the page.

| Category | Questions |
|---|---|
| Sales & growth | How do today's sales compare to yesterday? · What sold fastest this week? · What was our best sales day this month? · How are categories doing vs. the same period last year? · Which high-margin items should we upsell? |
| Restock & purchasing | What should I reorder today? · Which items are below minimum with no PO on the way? · What's out of stock but still selling? · What will run out in the next 7 days? · Which purchase orders are overdue? |
| Customers | Who are our top 10 customers this month? · Any large orders today? · Which customers haven't bought in 60+ days? · What quotes are still open? |
| Margin & pricing | How does today's margin compare to the last 30 days? · Which items have the highest markup and are still selling? · Which items are below our margin target? |
| Staff & operations | How are reps performing today? · Which register is busiest today? · How many tickets today have no sales rep assigned? |
| Inventory cleanup | How much is tied up in dead stock (no sales in 90 days)? · Which items haven't sold in 90 days? |
| Cash & end of day | What's collected today by payment type (including tips)? · How much is still sitting in unposted tickets today? · What layaways are open and what's the balance due? |

Some answers work a little differently than you might expect:

| Question | How it's answered |
|---|---|
| Below margin target | CounterPoint doesn't store a margin target, so each item is compared to your own average margin over the last 30 days. Give Claude a target % in chat if you have one. |
| Today's margin | Uses posted tickets only, since open tickets don't have cost yet. |
| Large orders today | There's no set "large order" dollar amount, so today's biggest orders are shown ranked. |
| Lapsed customers | Based on each customer's last posted ticket. Cash sales aren't counted. |
| Reorder today | Vendor comes from the item's vendor setup. A blank vendor means none is on file. |

If item sales answers look empty or out of date, ask Claude to "enable required data aggregation types." It will check CounterPoint's data aggregation tasks and walk you through turning them on.

## Section 9: Adding Custom Dashboards and Questions

| Option | Behavior |
|---|---|
| Add custom dashboard | Saves a shortcut card. When you tap it, Claude builds the answer fresh in chat each time. |
| Add custom question | Sends your question to chat. Claude writes and checks the query, then saves it to your list. Tap **Refresh** to see it. |

Custom items are saved in Barton and shared with everyone connected to your company's Barton. They never replace the prebuilt dashboards and questions.

## Section 10: Asking Good Questions

You'll get better answers if you include:

- **The store or location**, if you have more than one
- **The date range** ("yesterday", "last 30 days", "Q3 2026")
- **What to compare it to**, if anything ("vs. same day last year")
- **Your own rules**, if you have them. For min/max or forecasting, tell Claude exactly how to weigh sales history, seasonality, and item priority, or ask it to propose a method and explain its assumptions before you use the results.

Examples:

```
What were net sales for each store yesterday?
Show me the top 10 customers by net sales this month at the Madison store.
How did this week compare to the same week last year?
Look up item 10423. What's on hand at each location?
Which items are running low at all stores?
Show me all customers who had regular purchase history, then dropped off and haven't bought in 30 days.
Which CounterPoint tables and fields hold vendor item costs?
Suggest new min/max levels using the last 12 months of sales, weighted toward my priority items. Explain your method first.
Which Counterpoint report should I run to see sales by salesperson?
```

## Section 11: How to Read the Numbers

Barton follows the same rules as CounterPoint's standard reports (Sales Analysis by Store, Flash Sales Report, Sales History by Customer).

| Term | Meaning |
|---|---|
| Net sales | Before tax. This is what CounterPoint reports show and what Barton uses by default. |
| Gross / total | Includes tax. Will run higher than CounterPoint reports by the tax amount. |
| Posted | Tickets that have been closed and posted to history. |
| Unposted / in progress | Open tickets that haven't posted yet. Can include stale tickets that were never closed. |
| Returns | Netted against sales, same as CounterPoint reports. |

"Today" figures combine posted and in-progress tickets. Earlier days use posted tickets only.

If a number doesn't match a CounterPoint report, check that you're comparing the same thing (net vs. gross, posted only vs. posted plus open).

## Section 12: Reports, Exports, and Scheduled Reporting

Barton supplies the data. Claude handles how it's presented, whether that's a table in chat, a web page, a PDF, an Excel file, or a downloadable CSV.

### Crystal Reports

Barton and Claude can't create Crystal Reports. Crystal is licensed SAP software, and reports have to be built in the licensed Crystal Report Designer. Claude and Barton can walk you through building one in the Designer and tell you exactly which views and tables to use. In many cases it's easier to skip Crystal and have Claude produce the report as a PDF, Excel file, or web page instead. For example, a drawer summary report can be recreated from a sample PDF.

### Large Exports

Answers are limited to 500 rows (see Section 3). For bigger data sets, ask Claude for the SQL query and step-by-step instructions to pull the full data set into Excel.

### Scheduled and Recurring Reports

Barton doesn't schedule or send reports. Scheduling is handled by Claude, if your plan supports scheduled tasks. Keep in mind:

- Every AI-driven refresh uses tokens and counts against your Claude plan's usage limits. A report that refreshes many times a day can use up a plan quickly.
- For reports that run on a schedule (for example, sent to a group every day at noon), the better option is to have Claude help build a permanent report that pulls from an API. After that, refreshes don't use the AI at all.
- That report can use the NCR CounterPoint API you may already have, or Rapid's separate API suite. Contact Rapid to talk through options.

## Section 13: Writing Data Back to CounterPoint

Barton is read-only on purpose, and Rapid does not recommend enabling write access. You stay in control of every change to CounterPoint. When an analysis produces new values, such as updated min/max levels, use one of these options:

| Option | How it works |
|---|---|
| CounterPoint import (recommended for most) | Ask Claude for an import-ready CSV file and instructions. Bring it in with CounterPoint's **Data Interchange** (more flexible) or **Data Import**. |
| Rapid API suite | A separate Rapid product for building tools that write data while following CounterPoint's business logic. Claude can use it alongside Barton for more advanced builds. Not required to use Barton. |
| SQL or API updates | For IT staff and developers only. Changes should go through your own review and controls. |

Before committing any import, run CounterPoint's **Verify** option. It simulates the import and shows errors before anything changes. Always review Claude's file and assumptions first. A person should always be the final approver.

> **Note:** APIs are developer-level tools. Used incorrectly, they can cause problems or affect performance just like a heavy query.

## Section 14: Barton Tools for Developers

If you're building your own dashboards, reports, or apps with Barton, these are the tools it makes available to Claude.

| Tool | Used for |
|---|---|
| `cp_whoami` | Identifies the user and which companies they can see |
| `cp_query_sql` | Runs read-only SQL against the CounterPoint database (requires `companyAlias`) |
| `cp_render_widget` | Renders a prebuilt dashboard |
| `cp_list_guided_prompts` | Returns the question list and categories shown in the guide |
| `cp_add_guided_prompt` | Saves a custom dashboard card or question |
| `cp_answer_guided_prompt` | Answers a saved question using its stored query |

Queries can't use `SELECT *`. Name the columns you need.

### Query Rules

Follow these rules in custom queries so results match CounterPoint reports:

- **Today's sales** combine posted history (`PS_TKT_HIST` / `PS_TKT_HIST_LIN`) with open tickets (`PS_DOC_HDR` / `PS_DOC_LIN`).
- **Net sales** use `SUB_TOT`, not `TOT`.
- **Line types** `S`, `A`, and `R` are all included so returns net out.
- **Location columns** differ by table (`STK_LOC_ID`, `PRC_LOC_ID`, etc.).
- **Item lookups** check both `IM_ITEM.ITEM_NO` and `IM_ITEM_ALIAS`, since CounterPoint treats them as one lookup.

## Section 15: Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "Client not found" when connecting | Something was typed in the OAuth Client ID or Client Secret fields | Remove the connector and add it again with the URL only. |
| Barton shows as failed to connect | Connector endpoint or Rapid CP API unreachable | Retry. If it persists, contact Rapid Support. |
| Answers stop mid-session or show an auth error | Sign-in timed out | Reconnect Barton from your Connectors list. |
| Requests are refused | Your Barton sign-in isn't set up for your company | Contact Rapid Support. |
| A question was refused | It asked for protected data | Ask for totals or counts instead (Section 2). |
| Numbers don't match a CounterPoint report | Comparing different things (net vs. gross, posted vs. open) | See Section 11, then ask Claude to show the query. |
| Today's numbers look high | Old open tickets that were never closed | Compare to the Flash Sales Report and close or void old tickets. |
| Answer stops at 500 rows | Per-answer row limit | Narrow the date range, store, or category, or ask for an Excel export query (Section 12). |
| Some customer details are hidden | Personal information protection | Expected. Ask for counts or totals instead (Section 2). |
| Answer takes a while | Analytical questions need several lookups | Expected. Simple questions return in seconds; deeper analysis takes longer. |
| Server slows down during heavy use | Several large queries running at once | Spread out heavy analysis, especially during business hours. |
| Running out of Claude plan usage | Plan limits, or frequent scheduled refreshes | Upgrade the Claude plan (Pro suits most users), or move recurring reports to an API (Section 12). |
| A result is wrong or something expected is missing | AI interpretation or data issue | Save the question, the answer, and what you expected, and send them to Rapid Support. |

## Section 16: Pricing and Subscription

| Item | Cost | Covers |
|---|---|---|
| One-time installation | $299 (non-refundable) | Onboarding, installation, configuration, and the CounterPoint connection |
| Monthly subscription | $99 per month, month-to-month, cancel anytime | Unlimited users, updates deployed by Rapid, compatibility maintenance, and new general-purpose skills. Rapid does not cap queries. |
| Claude plan | $0 to $20 per person per month, billed by Anthropic | Each user's own Claude account. Free suits occasional use, Pro suits most users, heavy users may need more. |
| Custom development | $156 per hour, T&M | Custom skills, dashboards, user-level access restrictions, or write tools. Many skills take about 30 minutes ($78). Estimates are approved before work begins. |

## Section 17: Frequently Asked Questions

### The Basics

**What is Barton?**

Barton is Rapid's MCP connector for NCR CounterPoint. It gives Claude read-only access to your CounterPoint data, along with the CounterPoint Data Dictionary, documentation, and Rapid's CounterPoint knowledge, so Claude can understand what the data means.

**Is Barton AI?**

No. Barton is a connector to AI. Claude is the brain that does the reasoning. Barton is the brawn that safely pulls the data and gives Claude the context to ask the right questions.

**Why can't Claude do this on its own?**

Claude has no access to your private business systems and doesn't know how CounterPoint data is structured. Barton supplies both the access and the CounterPoint knowledge, such as which tables hold inventory and how transfers differ from sales.

**What data can Barton see?**

Everything in your CounterPoint SQL database. If it lives in the database, Barton can show it to Claude, with the exception of protected personal information (see [Security and Access](#security-and-access)).

**Where do I use Barton? Is it in the CounterPoint menus?**

No. You use Barton from Claude's website or desktop app, on a computer, phone, or tablet.

**Can Barton pull in data from Klaviyo, Mailchimp, or other systems?**

No. Barton only works with CounterPoint. Claude would need its own separate connection to the other system. For example, you could use Barton to pick a list of customers and then build the email list in Klaviyo.

**What can I use it for?**

- Sales reporting by store, category, rep, or customer
- Finding which tables and fields hold the data you need
- Suggested min/max levels, forecasting, and purchasing or transfer advice
- Marketing lists, like "customers who bought regularly, then stopped buying 30 days ago"
- Recreating reports in PDF, Excel, or web formats
- Building custom dashboards, saved queries, and tools

**Who is it for?**

| Role | How Barton helps |
|---|---|
| Store owners | A full view of the business without waiting on reports |
| Operations managers | Early warning on stock issues |
| Buyers | See what sold, and where, before ordering |
| Multi-site retailers | Cross-location visibility without logging into each system |
| IT staff and developers | Build custom dashboards and saved queries for the rest of the team, without writing reports from scratch |

### AI Assistants and Accounts

**Which AI assistant should I use?**

Claude, by Anthropic. Rapid builds for, tests, and supports Claude.

**Does it work with ChatGPT or other AI assistants?**

Other MCP-compatible assistants may work, but they're untested and not supported. Claude is the only AI assistant Rapid builds for, tests, and supports.

**Does a Claude account give me access to Barton?**

Not on its own. Rapid creates each person's Barton sign-in. Contact Rapid Support to add someone.

**Can we share one Claude account?**

We don't recommend it. Claude personalizes answers using past conversations, so a shared account mixes everyone's history together.

**Which Claude model should I use?**

The standard models (such as Sonnet) handle most questions well. Higher-reasoning models are available but use more of your plan.

### Security and Access

**Does Barton follow CounterPoint user security?**

No. Anyone connected to Barton can see all sales, cost, profit, inventory, and customer data across every store, whatever their CounterPoint role. Treat Barton as an admin-only tool and only connect people authorized to see everything.

**Can access be limited by job function or user?**

Yes, as custom development. Contact Rapid for a quote.

**Is personal information protected?**

Yes. Birth dates, driver's license and tax IDs, card and bank numbers, and system credentials can't be returned at all. Customer addresses, emails, and phone numbers are limited to about 5 rows per answer.

**Is my data secure?**

Barton works on request only. It fetches just what each question needs, Rapid stores nothing on its servers, and Barton sends nothing to Claude unless asked. Anthropic's privacy policies cover how Claude handles data.

### Accuracy

**How accurate are the results?**

The numbers Barton retrieves are accurate. Claude's interpretation can be wrong, such as reading sales as gross instead of net, mishandling returns, or misreading a date range. Trust, but verify, especially for purchasing, staffing, and financial decisions.

**How can I check an answer?**

Ask Claude to "show the query" or "explain how you calculated that." The SQL behind every answer is available to review. You can also compare against the matching CounterPoint report.

**What if I get a wrong answer?**

Save the question, the answer, and what you expected, and send them to Rapid Support.

### Limits and Performance

**Is there a limit on how much data I can get?**

Each answer returns up to 500 rows (configurable). That doesn't limit what Barton can analyze. It can still total three years of sales by day. You just won't get more than 500 rows back in one answer. For a full export, ask Claude for the query and steps to build it in Excel.

**Can Barton slow down my CounterPoint server?**

Barton blocks very large queries that could hurt performance. Several users running big, complex questions at the same time can still slow the server, the same way several large SQL reports running at once would.

**Are results instant?**

Simple questions come back in seconds. Analytical questions take several lookups, so they take longer.

**Does Barton monitor my server or SQL Server health?**

No. Barton only reads CounterPoint data.

### Reports and Scheduling

**Can Barton build Crystal Reports?**

No. Crystal is licensed SAP software, and reports have to be built in the licensed Crystal Report Designer. Claude and Barton can guide you through it and tell you which views and tables to use. Often it's easier to have Claude produce the report as a PDF, Excel file, or web page instead.

**Can Barton send a report every day at a set time?**

Barton doesn't schedule anything. Claude can run scheduled tasks on some plans, but every refresh uses your plan's usage. For recurring reports, have Claude help build a permanent report that pulls from an API (the NCR CounterPoint API or Rapid's API suite). Then refreshes don't use the AI at all.

**Can I download results?**

Yes. Ask for a CSV, Excel file, or PDF.

### Changing CounterPoint Data

**Can Barton update CounterPoint?**

No. Barton is read-only on purpose, and Rapid doesn't recommend enabling write access.

**Then how do I get new min/max levels (or other changes) into CounterPoint?**

Ask Claude for an import-ready CSV and instructions, then bring it in with CounterPoint's Data Interchange or Data Import. Run the **Verify** option first to catch errors before anything changes. For tools that write data automatically, Rapid's separate API suite follows CounterPoint's business logic.

**Do I need the Rapid API to use Barton?**

No. The Rapid API suite is a separate product. It's only needed if you want to build tools that write data.

### Pricing and Billing

**How much does Barton cost?**

A one-time, non-refundable installation fee of $299 and $99 per month, month-to-month, cancel anytime.

**Is there a per-user charge?**

No. Barton includes unlimited users. Each user's Claude subscription is separate and billed by Anthropic.

**What does the monthly subscription cover?**

Unlimited users, updates deployed by Rapid, compatibility maintenance, and new general-purpose skills. Rapid doesn't cap queries. Your Claude plan has its own usage limits.

**What about custom work?**

Custom skills, dashboards, user-level access restrictions, and write tools are billed T&M at $156 per hour. Many skills take about 30 minutes. Estimates are approved before work begins.

**Will Barton improve over time?**

Yes. Rapid deploys improvements automatically to subscribers with no reinstall or upgrade fees.

### Getting Started

**What are the system requirements?**

CounterPoint 8.5.6.2 or later, SQL Server 2016 or later in Mixed Mode authentication, Windows Server 2016 or Windows 11 Pro, PowerShell 5.1, and outbound HTTPS access from the server. No inbound firewall changes are needed. Your environment must also meet Rapid's CI/CD Connector Requirements.

**How do I add users?**

Send Rapid one email with the names and email addresses of everyone who should have access. Only include people authorized to see all CounterPoint data. 
Note that users of "Barton" added during deployment are covered under the one-time setup fee. However, time spent to add users after the initial MCP AI Connector deployment will be billed at T&M for the time it takes to make the new connections.

**How do I connect?**

After installation, Rapid sends a welcome email with your connector URL, a username, a temporary password, and step-by-step instructions for Claude. Leave any OAuth Client ID or Secret fields blank when adding the connector. See Section 5.

**What if I get a "Client not found" error?**

Something was typed in the OAuth Client ID or Client Secret fields. Remove the connector and add it again with the URL only.

**Who do I contact for help?**

Rapid Support at 619-754-4100, or through the contact form at [rapidpos.com](https://rapidpos.com/counterpoint-ai-meet-barton/).

## Conclusion

The Rapid Barton MCP AI Connector gives Claude safe, read-only access to your CounterPoint data. With it you can get quick answers in plain English, use the Counterpoint AI Guide's prebuilt dashboards, analyze purchasing and inventory, or build custom tools on top of CounterPoint.

Before go-live, confirm your system meets the minimum requirements and choose your users carefully, since Barton shows all data to everyone connected. After deployment, compare a few key numbers against your CounterPoint reports and keep the trust-but-verify approach for anything important. Changes to CounterPoint always go through you.

For more answers, see Section 17. For help with connecting, custom dashboards or questions, reporting, or troubleshooting, contact Rapid Support at 619-754-4100 or through the contact form at [rapidpos.com](https://rapidpos.com/counterpoint-ai-meet-barton/).
