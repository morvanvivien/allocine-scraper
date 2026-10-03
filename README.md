# How to Scrape AlloCiné: French Cinema Movies, Ratings and Showtimes

> **Run it now, no code: [allocine-movies-scraper on the Apify Store](https://apify.com/bibian/allocine-movies-scraper)** &nbsp;|&nbsp; $2.00 per 1,000 movies &nbsp;|&nbsp; no browser, no proxy

AlloCiné is the main French site for cinema listings: movies now playing in French cinemas, new releases and upcoming films. It has no public export for third parties. This guide shows how to get that data as clean structured data: release date, duration, genres, directors, cast, ratings, synopsis, poster and showtimes count, with a free Apify Actor, from the console or from code.

Searches this page answers: allocine scraper, french cinema movies scraper, movie ratings data France, now playing movies API.

## What you get from the AlloCiné scraper

- Every movie currently playing, released this week, or most awaited upcoming, with title, release date and duration.
- Genres, directors and cast as arrays.
- Press and audience ratings when AlloCiné shows them, plus the number of showtimes found across French cinemas.
- Full synopsis and poster image URL.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | AlloCiné movie id and public link |
| `title` | Movie title |
| `releaseDate` | French theatrical release date |
| `durationMinutes` | Runtime in minutes |
| `genres` | Array of genres |
| `directors`, `cast` | Arrays of director and lead cast names |
| `pressRating`, `audienceRating` | AlloCiné ratings out of 5, `null` when not yet published |
| `showtimesCount` | Number of showtimes found across French cinemas, `null` for movies not yet released |
| `synopsis` | Full plot synopsis |
| `posterUrl` | Poster image URL |

## Use cases

- **Track new releases.** Pull the week's new releases every Wednesday (French release day) for a newsletter or a cinema-chain dashboard.
- **Build a "what's worth watching" widget.** Combine `audienceRating` and `showtimesCount` to surface the movies that are both well-rated and widely shown.
- **Monitor an upcoming title.** Watch a `coming-soon` entry until it gets a release date and showtimes.
- **Feed a film database or recommendation engine.** Use genres, directors and cast as structured tags.

## Input

| Field | What it does |
|---|---|
| `list` | `now-playing`, `this-week` (new releases) or `coming-soon` (most awaited upcoming). |
| `maxItems` | The run stops once this many movies are saved (default 50). |

Example input:

```json
{
  "list": "now-playing",
  "maxItems": 50
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/allocine-movies-scraper) and click **Try for free** (a free Apify account is enough).
2. Pick the list and the maximum number of results, then click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a weekly new-release alert, open **Schedules**, create a weekly schedule with `list` set to `this-week`, and optionally add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/allocine-movies-scraper').call({
    "list": "now-playing",
    "maxItems": 50
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.releaseDate} | ${item.genres.join(', ')} | ${item.showtimesCount ?? '-'} showtimes`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/allocine-movies-scraper").call(run_input={
    "list": "now-playing",
    "maxItems": 50,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('releaseDate')} | {item.get('showtimesCount')} showtimes")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$2.00 per 1,000 movies** (that is $0.002 per movie), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/allocine-movies-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- Only what AlloCiné publishes is collected. `pressRating` and `audienceRating` are often `null` for movies not yet released (visible in `sample-output.json`).
- `showtimesCount` reflects showtimes found at the time of the run; it changes daily as cinemas update their schedules.
- This is an unofficial tool, not affiliated with AlloCiné. Check the site terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads the data directly, so runs are fast and cheap.

**How do I get this week's new releases only?**  
Set `list` to `this-week` and schedule the Actor weekly on release day (Wednesday in France).

**Can I get upcoming movies before they're released?**  
Yes, set `list` to `coming-soon` for the most awaited upcoming films.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open allocine-movies-scraper on the Apify Store](https://apify.com/bibian/allocine-movies-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
