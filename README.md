# Half-Life — Caffeine Tracker

A single-file web app that estimates how much caffeine is in your system throughout the day, based on how much you've had and when, using a standard exponential decay (half-life) model.

Everything — HTML, CSS, and JavaScript — lives in one file: `index.html`. No build step, no dependencies.

## Put it on GitHub Pages

1. Create a new GitHub repo (or use an existing one).
2. Add `index.html` to the root of the repo.
3. Commit and push.
4. In the repo, go to **Settings → Pages**.
5. Under **Build and deployment**, set **Source** to `Deploy from a branch`, pick your branch (usually `main`) and `/ (root)` as the folder.
6. Save. GitHub will give you a URL like `https://your-username.github.io/your-repo/` within a minute or two.

That's it — no server, no npm install.

## How the math works

Caffeine leaves your bloodstream roughly exponentially. For each drink logged, the app calculates:

```
remaining = dose_mg × 0.5 ^ (hours_since_consumed / half_life_hours)
```

The "current level" figure is the sum of the remaining caffeine from every drink logged today (and yesterday, if it hasn't fully cleared). The chart plots this same formula across a 24-hour window.

## Making adjustments

Open `index.html` and look for the `CONFIG` object near the top of the `<script>` block — it's the only place you should need to touch for common tweaks:

```js
const CONFIG = {
  HALF_LIFE_HOURS: 5,       // caffeine half-life used for decay (3–7h is a typical range)
  DAILY_LIMIT_MG: 400,      // guideline shown on the chart and status pill
  PRESETS: [ ... ],         // quick-add drink chips: name, mg, size label
  CHART_POINTS: 144,        // resolution of the decay curve
};
```

Things you might want to change:

- **Half-life**: some people metabolize caffeine faster or slower (genetics, pregnancy, medication, smoking status all affect it). Adjust `HALF_LIFE_HOURS`.
- **Daily guideline**: change `DAILY_LIMIT_MG` if you're working from a different recommendation.
- **Presets**: add, remove, or edit entries in `PRESETS`. Each needs `name`, `mg`, and `size`.
- **Colors/fonts**: all in the `<style>` block, defined as CSS variables at the top (`--espresso`, `--caramel`, etc.) so you can re-theme without hunting through the CSS.

## Persistence (saving your log between visits)

By design, this file keeps your log only in memory for the current page load — it deliberately avoids `localStorage` so it behaves consistently in sandboxed previews. Once it's running on your own GitHub Pages site, you can make it remember your log across visits by editing two functions near the top of the script:

```js
function loadDrinks(){
  const raw = localStorage.getItem('caffeine-log');
  return raw ? JSON.parse(raw) : [];
}

function saveDrinks(){
  localStorage.setItem('caffeine-log', JSON.stringify(drinks));
}
```

Swap in those two versions (they're already sketched out as comments in the file) and your log will persist in each visitor's browser.

## Disclaimer

Caffeine content per drink and half-life both vary by person and product. This tool gives estimates for personal tracking, not medical advice.
