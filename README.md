# Job Search MCP: newest jobs from 25,500+ company career sites

An MCP tool that lets **Claude, ChatGPT, Cursor, VS Code and any AI agent** search open jobs straight from employers' own career sites: Greenhouse, Lever, Ashby, Workday, SmartRecruiters, Workable, Recruitee, Personio, Teamtailor, BambooHR, Breezy, Rippling, Oracle, Pinpoint and Jobvite.

One plain-language query in (`"remote senior python developer in Berlin"`), the **newest matching jobs** out, as compact JSON with the salary, remote status, seniority, posting date and the employer's apply link.

It is hosted: nothing to install or run. The tool is the [Job Search API for AI Agents](https://apify.com/richiedc95/job-search-api) Actor on Apify, served through Apify's MCP server.

## Connect in 30 seconds

**Remote MCP URL** (sign in with your Apify account when your client asks):

```text
https://mcp.apify.com?tools=richiedc95/job-search-api
```

**Claude Code**

```bash
claude mcp add --transport http job-search "https://mcp.apify.com?tools=richiedc95/job-search-api"
```

**Cursor, Claude Desktop, VS Code and other JSON configs** (with an Apify API token instead of OAuth):

```json
{
    "mcpServers": {
        "job-search": {
            "url": "https://mcp.apify.com?tools=richiedc95/job-search-api",
            "headers": { "Authorization": "Bearer <YOUR_APIFY_TOKEN>" }
        }
    }
}
```

Get a token in Apify Console under **Settings → API & Integrations**.

## Ask your agent

- "Find remote senior data engineer jobs posted this week and list the ones that publish a salary."
- "Which companies are hiring product designers in London right now?"
- "Find 10 junior frontend developer jobs in Germany that sponsor visas."
- "Compare salaries of the newest staff engineer openings at Stripe, Airbnb and Datadog."

## Tool input

| Field | What it does | Default |
|---|---|---|
| `query` | **Required.** Job title, role or skill, e.g. `data engineer`. Words match in any order. Separate alternatives with commas. Plain language such as `remote nurse in Texas` works. | |
| `location` | City, country or region the job location must mention, e.g. `London`, `Germany`, `US`. | anywhere |
| `remoteOnly` | Only fully remote jobs. | `false` |
| `postedWithinDays` | Only jobs posted in the last N days (0 = any date). | `30` |
| `seniority` | `intern`, `junior`, `mid`, `senior`, `lead`, `manager`, `director` or `executive`. | any |
| `minSalary` | Minimum yearly pay; jobs without a published salary are skipped. | `0` |
| `visaSponsorship` | Only jobs whose description offers visa sponsorship. | `false` |
| `company` | Only these companies, e.g. `Stripe, Airbnb`. | all |
| `maxResults` | How many jobs to return, newest first (1-200). | `20` |
| `maxPerCompany` | At most this many jobs per company. | `3` |
| `includeDescription` | Add the description as plain text (up to 3,000 characters). | `false` |

## Output

One record per job, newest first:

```json
{
    "title": "Senior Data Engineer",
    "company": "Acme Analytics",
    "location": "Remote - US",
    "remote": true,
    "workplaceType": "remote",
    "seniority": "senior",
    "employmentType": "full-time",
    "salary": "USD 160,000-190,000 per year",
    "salaryMin": 160000,
    "salaryMax": 190000,
    "salaryCurrency": "USD",
    "salaryPeriod": "year",
    "postedAt": "2026-11-04",
    "url": "https://jobs.lever.co/acme/1a2b3c",
    "visaSponsorship": null,
    "ycBatch": "W21",
    "source": "lever"
}
```

About 130 tokens per job, so 20 results fit easily in any context window.

## Use it without MCP

It is also a plain HTTP API. One call runs the search and returns the jobs:

```bash
curl -X POST "https://api.apify.com/v2/acts/richiedc95~job-search-api/run-sync-get-dataset-items" \
  -H "Authorization: Bearer <YOUR_APIFY_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"query": "senior data engineer", "remoteOnly": true, "postedWithinDays": 7, "maxResults": 20}'
```

Python:

```python
from apify_client import ApifyClient

client = ApifyClient("<YOUR_APIFY_TOKEN>")
run = client.actor("richiedc95/job-search-api").call(run_input={"query": "registered nurse", "location": "Texas", "maxResults": 20})
for job in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(job["postedAt"], job["title"], job["company"], job["url"])
```

### No-code: n8n, Make and Zapier

Get daily job alerts in Slack, Google Sheets or email without writing code.

- **n8n:** import a ready-made workflow from [`examples/`](examples/):
  - [daily alerts with every filter, each job sent once](examples/n8n-daily-job-alerts-slack.json);
  - [a plain-language daily digest](examples/n8n-job-search-api-slack.json).
- **Make and Zapier:** step-by-step scenarios with the official Apify app are in [examples/README.md](examples/README.md).

## How it works

- A directory of 25,500+ companies and their job boards is built from public sources and refreshed regularly.
- Every job title of every company is indexed daily, so a search checks all of them in memory in seconds.
- Companies are visited newest matching posting first, and every matching job is fetched live from the employer's career site, so closed jobs are not returned.
- Only public job board endpoints that companies publish for anyone: no login, no personal data, no LinkedIn or Indeed scraping.

A typical 20-job search takes about 10 seconds.

## Pricing

Pay per result on Apify: **$4 per 1,000 jobs** plus about $0.01 per GB of memory when a run starts, so a 20-job search costs about $0.10. There is no subscription to this tool, and `maxResults` caps every call.

## More tools from the same data

- [Job Search Scraper: 25,500+ Company Career Sites](https://apify.com/richiedc95/company-jobs-search): every filter and full job records, for bulk exports and job boards.
- [Career Site Jobs Scraper](https://apify.com/richiedc95/career-site-jobs-scraper): all jobs from the companies you choose.
- [Greenhouse](https://apify.com/richiedc95/greenhouse-jobs-scraper), [Lever](https://apify.com/richiedc95/lever-jobs-scraper), [Ashby](https://apify.com/richiedc95/ashby-jobs-scraper), [Workday](https://apify.com/richiedc95/workday-jobs-scraper) and [SmartRecruiters](https://apify.com/richiedc95/smartrecruiters-jobs-scraper) job scrapers.

## Feedback

Found a bug or want a filter? Open an issue here or on the Actor's Issues tab.

## License

The documentation and examples in this repository are MIT licensed.
