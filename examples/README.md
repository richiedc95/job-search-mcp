# No-code job alerts: n8n, Make and Zapier

Ready-made workflows that search 25,500+ company career sites every morning and post the new jobs to Slack. They call the Apify API directly, so they work without MCP or an AI agent.

## n8n templates

| File | Actor | What you get |
|---|---|---|
| [`n8n-daily-job-alerts-slack.json`](n8n-daily-job-alerts-slack.json) | [Job Search Scraper](https://apify.com/richiedc95/company-jobs-search) | Every filter, with `onlyNewJobs`: each job is posted once, never twice. |
| [`n8n-job-search-api-slack.json`](n8n-job-search-api-slack.json) | [Job Search API for AI Agents](https://apify.com/richiedc95/job-search-api) | One plain-language query (`"senior python developer, data engineer"`) and compact records, with the jobs posted in the last day. |

**Import:** in n8n open **Workflows → Import from URL** and paste the file's raw link, for example:

```text
https://raw.githubusercontent.com/richiedc95/job-search-mcp/main/examples/n8n-daily-job-alerts-slack.json
```

You can also download the file and use **Import from File**.

**Setup (2 minutes):**

1. Create a free [Apify](https://apify.com) account and copy your API token (Console → **Settings → API & Integrations**).
2. In the **Search** node, add a **Header Auth** credential: Name `Authorization`, Value `Bearer <your token>`.
3. Connect Slack in the **Post to Slack** node and pick your channel.
4. Edit the keywords and locations in the request body.

**Cost:** you pay Apify per job returned, and the request body caps every run.

- The Job Search Scraper costs $3 per 1,000 jobs, plus about $0.02 per run. At the 50-job cap that is at most about $5 a month. It is usually less, because only new jobs are returned and charged.
- The Job Search API costs $4 per 1,000 jobs, plus about $0.02 per run. At 20 jobs a day that is about $3 a month.

Apify's free plan includes monthly credit that covers light use.

## Variants

Change the URL or the body of the **Search** node:

| Goal | Actor in the URL | Body changes |
|---|---|---|
| Remote jobs only | `richiedc95~remote-jobs-search` | same body |
| Startups and Y Combinator companies | `richiedc95~startup-jobs-search` | `"ycOnly": true` |
| Lead list of hiring companies | `richiedc95~company-jobs-search` | `"outputMode": "companies"`. Send the rows to Google Sheets instead of Slack. |
| Government tenders (UK, EU, Canada, Colombia) | `richiedc95~public-tenders-scraper` | `{"keywords": ["software"], "onlyNewTenders": true}` |
| News and brand monitoring | `richiedc95~global-news-scraper` | `{"queries": ["your brand"], "onlyNewArticles": true}` |

## Make (Integromat)

Build this scenario with Make's official **Apify** app:

1. **Schedule:** every day at 08:00.
2. **Apify → Run an Actor:**
   - Actor `richiedc95/company-jobs-search`.
   - Input: the JSON body from the n8n template.
   - Wait for finish: **Yes**.
3. **Apify → Get Dataset Items:** read the run's default dataset.
4. **Iterator.**
5. Send each job to one of these:
   - **Slack → Create a Message**;
   - **Google Sheets → Add a Row** (title, company, location, salaryMax, url);
   - **Email.**

## Zapier

1. In Apify Console, create a **task** for `richiedc95/company-jobs-search` with `"onlyNewJobs": true`, and give it a daily schedule.
2. In Zapier, use the official **Apify** app:
   - Trigger: **Finished Actor Run**, pointed at that task.
   - Action: **Fetch Dataset Items**.
3. Add a Slack, Google Sheets, Gmail or CRM step.

## Field reference

| Template | Fields |
|---|---|
| Job Search Scraper | `title`, `company`, `location`, `salaryMin`, `salaryMax`, `salaryCurrency`, `applyUrl`, `url`, `postedAt`, `remote`, `seniority` and more |
| Job Search API | the compact record shown in the [main README](../README.md#output) |
