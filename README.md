# Set It & Forget It — Arkin family NFL pool

A draft board and live season tracker for the family "set it and forget it" league:
eight brothers, a snake draft, four NFL teams each, all 32 teams gone.

Everything is one static page. No build step, no server, no API key, no accounts.

---

## Publish it (about 3 minutes)

1. Go to **github.com/new**. Name the repo `sifi` (or whatever you like). Set it to
   **Public** — GitHub Pages needs public on a free account. Don't add a README;
   this folder has one. Click **Create repository**.
2. On the empty repo page, click **uploading an existing file**. Drag in
   `index.html`, `rosters.json`, `recaps.json` and this `README.md`. Click
   **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment*, set **Source** to
   *Deploy from a branch*, **Branch** to `main` and the folder to `/ (root)`. Save.
4. Wait a minute, then reload that Settings → Pages screen. It will show your URL:

   ```
   https://<your-github-username>.github.io/sifi/
   ```

That link is the one to send the family. It works on any phone, no login.

Every later change is the same move: edit the file on GitHub (pencil icon), commit,
and the site updates in under a minute.

---

## After the draft: turn the season on

The Season and Leaderboard tabs stay locked until the site knows who owns what.

1. Open the site, run the draft on the **Draft** tab.
2. When the 32nd pick lands, hit **Results**, choose the **rosters.json** tab, and
   click **Download rosters.json** (or **Copy** and paste it by hand).
3. In the repo, click `rosters.json` → pencil icon → replace everything with the new
   contents → **Commit changes**.

That's it. Season and Leaderboard unlock and start filling in on their own.

`rosters.json` looks like this:

```json
{
  "order": ["Jeff", "Dave", "Mike", "Steve", "James", "Joe", "Greg", "Andy"],
  "rosters": {
    "Jeff": ["BAL", "HOU", "ARI", "NO"],
    "Dave": ["PHI", "LAC", "MIN", "NYJ"]
  }
}
```

`order` is the draft order. Each roster is four team codes.

---

## Where the scores come from

The page calls ESPN's public NFL scoreboard endpoint straight from the browser:

```
https://site.api.espn.com/apis/site/v2/sports/football/nfl/scoreboard?dates=2026&seasontype=2&week=N
```

No key, no signup, no rate limit worth worrying about. On load it walks weeks 1
through whatever week it is, counts only **completed** games, and works out each
brother's W-L from his four teams.

- Games still in progress are not counted; the header says how many are left.
- Bye weeks are skipped, not scored as losses.
- Ties count as ties, the way the family spreadsheet has always done it.
- Finished weeks are cached in the browser, so reloading is one request, not
  eighteen.

If ESPN is unreachable, the page falls back to whatever results are baked into the
`season-data` block inside `index.html` and just doesn't show the live badge.

---

## Weekly recaps (optional)

`recaps.json` holds the funny line at the top of the Season tab, keyed by week
number:

```json
{
  "1": "Greg opened 4-0 while everyone argued the draw was rigged. It was not.",
  "2": "James went 0-4 and reminded the group chat it is a long season. It is week 2."
}
```

Any week without a line gets an automatic factual one instead — games final, best
week, worst week. Add lines whenever you feel like it; you never have to.

---

## Team codes

```
AFC East   BUF NE  MIA NYJ        NFC East   PHI DAL WAS NYG
AFC North  PIT BAL CIN CLE        NFC North  CHI GB  MIN DET
AFC South  JAX HOU IND TEN        NFC South  CAR TB  ATL NO
AFC West   DEN LAC KC  LV         NFC West   SEA LAR SF  ARI
```

Note: ESPN calls Washington `WSH`. The page translates it to `WAS` for you — use
`WAS` in `rosters.json`.

---

## Running it without GitHub

`index.html` is entirely self-contained. Double-click it and it works — draft board,
live scores and all. `rosters.json` and `recaps.json` only load when it's served over
http, so from the desktop you'd draft in the page rather than reading rosters from a
file.
