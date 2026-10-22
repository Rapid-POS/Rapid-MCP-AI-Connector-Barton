# Rapid POS Barton MCP AI Connector

**Updated October 22nd 2026**

The Rapid Barton MCP AI Connector gives Claude live, read-only access to your CounterPoint data. Ask questions in plain English, like "What are my sales today versus the same day last year?", or build your own dashboards and apps on top of CounterPoint. Barton is a custom-built MCP (Model Context Protocol) server with retail and CounterPoint skills built in. Along with the data, it gives the AI the context it needs to read that data correctly.

> **Note:** Barton is read-only. It can't add, change, or delete anything in CounterPoint.

## Overview

Barton connects Claude to your CounterPoint database through the Rapid CP API. Claude is the AI "brain" that understands your question and explains the answer. Barton is the "brawn" that runs the queries safely and passes along the CounterPoint knowledge Claude needs. Barton ships with the **Counterpoint AI Guide**, a set of prebuilt dashboards and questions tested to match CounterPoint's own reports. Every query runs under built-in guardrails: it's read-only, written to avoid database lockups, and capped on how many rows it can return. Barton works anywhere Claude does, on a desk, phone, or tablet, and updates are delivered automatically to subscribers.

### Minimum System Requirements

- Minimum Counterpoint version: 8.5.6.2
- Minimum SQL Server version: 2016
- Minimum Supported Operating System version: Windows Server 2016 or Windows 11 Pro
- Minimum PowerShell version: 5.1

> **Warning**
>
> Your environment must meet our CI/CD Connector Requirements (server access, firewall rules, etc.) before any install or upgrade. Troubleshooting, manual installs, or follow-up work resulting from unmet requirements will be billed at standard T&M rates.
>
> If your system does not meet these minimum requirements, please consult your Care Team Lead (vCIO) for an upgrade quote.

You'll also need an active Claude account that supports custom connectors (Free, Pro, Max, Team, or Enterprise). The Free plan allows 1 custom connector, which is enough for Barton, but has tight usage limits. On Team and Enterprise plans, an account owner adds Barton once and each user then connects to it.

## Table of Contents

- [Section 1: What Barton Is](#section-1-what-barton-is)
- [Section 2: Data Access and Security](#section-2-data-access-and-security)
- [Section 3: Barton Guardrails](#section-3-barton-guardrails)
- [Section 4: Connecting Barton to Claude](#section-4-connecting-barton-to-claude)
- [Section 5: The Counterpoint AI Guide](#section-5-the-counterpoint-ai-guide)
- [Section 6: Prebuilt Dashboards](#section-6-prebuilt-dashboards)
- [Section 7: Prebuilt Questions](#section-7-prebuilt-questions)
- [Section 8: Adding Custom Dashboards and Questions](#section-8-adding-custom-dashboards-and-questions)
- [Section 9: Asking Good Questions](#section-9-asking-good-questions)
- [Section 10: How to Read the Numbers](#section-10-how-to-read-the-numbers)
- [Section 11: Barton Tools for Developers](#section-11-barton-tools-for-developers)
- [Section 12: Troubleshooting](#section-12-troubleshooting)
- [Section 13: Pricing and Subscription](#section-13-pricing-and-subscription)
- [Section 14: Frequently Asked Questions](#section-14-frequently-asked-questions)
- [Conclusion](#conclusion)

---

## Section 1: What Barton Is

Barton is a custom MCP server built by Rapid for CounterPoint. It isn't AI on its own. It doesn't think, guess, or make decisions. When an AI model asks for something, Barton supplies its tools, resources, and prompts and runs the queries.

| Role | Who | What it does |
|---|---|---|
| Brain | Claude | Understands your question, decides what to look up, and explains the answer |
| Brawn | Barton | Connects to CounterPoint, runs read-only queries, and supplies the retail and CounterPoint context needed to read the results |

```
Claude (brain)  →  Barton MCP server (brawn)  →  Rapid CP API  →  CounterPoint SQL database
```

### Supported AI Models

Claude (by Anthropic) is the only AI assistant Rapid builds for, tests, and supports. Barton uses the open MCP standard, so it may work with other MCP-compatible assistants such as ChatGPT or Perplexity, but those are untested and results may vary. Rapid does not provide troubleshooting or accuracy tuning for them. No specific Claude model needs to be selected; connectors work at the account level.

### Ways to Use Barton

Barton can be used two ways:

- **As an AI assistant.** Have simple conversations about your data, such as "What are my sales today versus the same day last year?"
- **As a builder's tool.** Developers can use Barton to build custom apps, dashboards, and reports for CounterPoint.

## Section 2: Data Access and Security

> **Important**
>
> Barton does not use CounterPoint User Security. Anyone you connect to Barton can see all sales, profit, cost, inventory, and customer data, whatever their role or security settings in CounterPoint. Only connect people who should see everything.

**Trust, but verify.** Claude can make mistakes. Rapid has done everything in our power to steer Claude to the correct answer every time. The numbers Barton retrieves are accurate, but Claude's interpretation can be wrong, for example with time ranges or unusual data. Always check decisions about purchasing, staffing, payroll, taxes, or financial reporting against CounterPoint directly.

### How Your Data Is Handled

- Barton works on a request-only basis. It fetches only the data a question needs.
- Your data stays in CounterPoint. Rapid does not store your data on its servers, and Barton sends nothing to Claude unless Claude asks for it.
- Claude's handling of data is governed by Anthropic's privacy policies.

If an answer looks off, ask Claude to "show the query" or "explain how you calculated that."

## Section 3: Barton Guardrails

Barton has guardrails built in to protect your CounterPoint system.

| Guardrail | Behavior |
|---|---|
| Read-only | Barton can't add, change, or delete CounterPoint data. |
| Safe queries | Every query is written so it won't lock up the database or slow down your registers. |
| Row and data limits | Each query has a hard limit on how much data it can return. Barton can't export "all your data." This keeps Claude from asking for too much at once and slowing CounterPoint down with a heavy query. |

Big or open-ended questions ("show me every ticket this year") will come back summarized or cut off at the row limit. Narrow the date range or store to get more detail.

> **Note:** Rapid is open to adding write access as custom work, but we strongly urge caution before giving an AI model the ability to change CounterPoint data. Contact Rapid to discuss.

## Section 4: Connecting Barton to Claude

After your deployment, Rapid sends you an email with your connector URL and sign-in details.

### Before You Start

- Have your connector URL ready. It looks like this:

```
  https://<your-company>-cp-mcp.rapidextras.com/mcp
```

- If you're asked for OAuth credentials, use the username and temporary password from your deployment email.

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

## Section 5: The Counterpoint AI Guide

The Counterpoint AI Guide is the starting screen for Barton. It shows:

- **Sales today** and **Items below minimum stock** at the top
- **Dashboard cards** on the left (see Section 6)
- **Questions that lead to action**, grouped by category (see Section 7)

Tap a card or question to see the answer on the page. You can also type a card's number in chat ("run 1") or just ask in your own words. If your install has more than one company, the guide asks which company to show first.

Questions about a specific location use your primary location. If you only have one location, that's the one used. If you have several, it's the location with the most sales over the last 90 days.

## Section 6: Prebuilt Dashboards

| # | Dashboard | What it shows |
|---|---|---|
| 1 | Store manager dashboard | Today's sales, staff, top customers, and inventory alerts in one live view |
| 2 | Inventory health | Low stock, out of stock, on order, and dead inventory for your primary location |
| 3 | Morning open | Yesterday's close, open orders, and what needs attention first |
| 4 | End-of-day summary | Today's totals, how the day compared, and what to act on tomorrow |
| 5 | Rep & customer performance | Who's selling, who's buying, and average ticket trends over the last 7 days |

## Section 7: Prebuilt Questions

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

## Section 8: Adding Custom Dashboards and Questions

| Option | Behavior |
|---|---|
| Add custom dashboard | Saves a shortcut card. When you tap it, Claude builds the answer fresh in chat each time. |
| Add custom question | Sends your question to chat. Claude writes and checks the query, then saves it to your list. Tap **Refresh** to see it. |

Custom items are saved in Barton, so everyone using your company's guide sees them.

## Section 9: Asking Good Questions

You'll get better answers if you include:

- **The store or location**, if you have more than one
- **The date range** ("yesterday", "last 30 days", "Q3 2026")
- **What to compare it to**, if anything ("vs. same day last year")

Examples:

```
What were net sales for each store yesterday?
Show me the top 10 customers by net sales this month at the Madison store.
How did this week compare to the same week last year?
Look up item 10423. What's on hand at each location?
Which items are running low at all stores?
How much did our top wholesale account buy last month, by category?
Has any category been declining for the last several weeks?
Which Counterpoint report should I run to see sales by salesperson?
```

## Section 10: How to Read the Numbers

Barton follows the same rules as CounterPoint's standard reports (Sales Analysis by Store, Flash Sales Report, Sales History by Customer).

| Term | Meaning |
|---|---|
| Net sales | Before tax. This is what CounterPoint reports show and what Barton uses by default. |
| Gross / total | Includes tax. Will run higher than CounterPoint reports by the tax amount. |
| Posted | Tickets that have been closed and posted to history. |
| Unposted / in progress | Open tickets that haven't posted yet. Can include stale tickets that were never closed. |
| Returns | Netted against sales, same as CounterPoint reports. |

If a number doesn't match a CounterPoint report, check that you're comparing the same thing (net vs. gross, posted only vs. posted plus open).

## Section 11: Barton Tools for Developers

If you're building your own dashboards or apps with Barton, these are the tools it makes available to Claude.

| Tool | Used for |
|---|---|
| `cp_whoami` | Identifies the user and which companies they can see |
| `cp_query_sql` | Runs read-only SQL against the CounterPoint database (requires `companyAlias`) |
| `cp_render_widget` | Renders a prebuilt dashboard |
| `cp_list_guided_prompts` | Returns the question list and categories shown in the guide |
| `cp_add_guided_prompt` | Saves a custom dashboard card or question |
| `cp_answer_guided_prompt` | Answers a saved question using its stored query |

### Query Rules

Follow these rules in custom queries so results match CounterPoint reports:

- **Today's sales** combine posted history (`PS_TKT_HIST` / `PS_TKT_HIST_LIN`) with open tickets (`PS_DOC_HDR` / `PS_DOC_LIN`). Posted uses `SUB_TOT`; open uses line `EXT_PRC`.
- **Net sales** use `SUB_TOT`, not `TOT`.
- **Line types** `S`, `A`, and `R` are all included so returns net out.
- **Item movement** uses `DM_SAL_STR_ITEM_DAY` (`SLS_QTY - RTN_QTY`).
- **Location columns** differ by table (`STK_LOC_ID`, `PRC_LOC_ID`, etc.).
- **Item lookups** check both `IM_ITEM.ITEM_NO` and `IM_ITEM_ALIAS`, since CounterPoint treats them as one lookup.
- **Open quotes** are `DOC_TYP = 'Q'` and **open layaways** are `DOC_TYP = 'L'` on `PS_DOC_HDR`.
- **Open POs** are `PO_STAT IN ('O','P')`.

## Section 12: Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Barton shows as failed to connect | Connector endpoint or Rapid CP API unreachable | Retry. If it persists, contact Rapid Support. |
| Answers stop mid-session or show an auth error | Sign-in timed out | Reconnect Barton from your Connectors list. |
| Numbers don't match a CounterPoint report | Comparing different things (net vs. gross, posted vs. open) | See Section 10, then ask Claude to show the query. |
| Today's numbers look high | Old open tickets that were never closed | Compare to the Flash Sales Report and close or void old tickets. |
| Answer looks cut off | Hit the per-query row limit | Narrow the date range, store, or category. |
| Answer takes a while | Analytical questions need several lookups | Expected. Simple questions return in seconds; deeper analysis takes longer. |
| Hitting usage limits in Claude | Claude plan limits (Free is tight) | Upgrade the Claude plan. Pro suits most users. |

## Section 13: Pricing and Subscription

| Item | Cost | Covers |
|---|---|---|
| One-time installation | $299 (non-refundable) | Onboarding, installation, configuration, and the CounterPoint connection |
| Monthly subscription | $99 per month, cancel anytime | Ongoing updates, compatibility maintenance, and new general-purpose skills. Rapid does not cap queries. |
| Claude plan | $0 to $20 per person per month, billed by Anthropic | Free suits occasional use, Pro suits most users, heavy users may need more |
| Custom development | $156 per hour, T&M | Custom skills, dashboards, or write access. Many skills take about 30 minutes ($78). Estimates are approved before work begins. |

## Section 14: Frequently Asked Questions

**Why can't Claude do this on its own?**

Claude has no access to your private business systems and doesn't know how CounterPoint data is structured. Barton supplies both the access and the CounterPoint knowledge, such as which tables hold inventory and how transfers differ from sales.

**How accurate are the results?**

The numbers Barton retrieves are accurate. Claude's interpretation can occasionally be wrong, so verify anything important against CounterPoint (see Section 2).

**Are results instant?**

Simple questions come back in seconds. Analytical questions take several lookups, so they take longer but give a more thorough answer.

**Is my data secure?**

Barton only fetches what each question needs, stores nothing on Rapid's servers, and sends nothing to Claude unless asked. Keep in mind that Barton does not use CounterPoint User Security, so anyone connected can see all data (see Section 2).

**Will it improve over time?**

Yes. Improvements ship automatically to subscribers with no reinstall or upgrade fees.

**Who is it for?**

| Role | How Barton helps |
|---|---|
| Store owners | A full view of the business without waiting on reports |
| Operations managers | Early warning on stock issues |
| Buyers | See what sold, and where, before ordering |
| Multi-site retailers | Cross-location visibility without logging into each system |
| IT staff and developers | Build custom dashboards and saved queries for the rest of the team, without writing reports from scratch (see Section 11) |

## Conclusion

The Rapid Barton MCP AI Connector gives Claude safe, read-only access to your CounterPoint data. With it you can get quick answers in plain English, use the Counterpoint AI Guide's prebuilt dashboards, or build custom tools on top of CounterPoint.

Before go-live, confirm your system meets the minimum requirements and decide who should have access, since Barton shows all data to everyone connected. After deployment, compare a few key numbers against your CounterPoint reports and keep the trust-but-verify approach for anything important.

For help with connecting, custom dashboards or questions, write access, or troubleshooting, contact Rapid Support at 619-754-4100 or through the contact form at [rapidpos.com](https://rapidpos.com/counterpoint-ai-meet-barton/).
