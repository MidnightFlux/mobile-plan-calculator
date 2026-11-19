# Mobile Plan Split Calculator

A single-file, offline web app that works out **how much each person really pays per month** for a mobile plan — main plan, sub-lines, a phone bought on contract, and a staff rebate — all rolled into one number.

Open `index.html` in a browser. That's it: no build step, no dependencies, no server, no network calls.

## Features

- **Cost per person per month**, plus whole-term totals for the length of the contract.
- **Sub-lines** (0–3) with their own monthly cost; the main line counts as a person.
- **Phone on contract** — compares retail price, selling price and contract price, and shows resale profit or loss. Under the selling price it also reports how far that price is below (or above) the retail price, as a percentage.
- **Annual rebate**, either shared evenly across everyone or applied to the **main line only**.
- **"Where the money goes"** step-by-step breakdown, with green marking money coming back.
- **Shareable links** — the current values are encoded in the URL; "Copy share link" puts it on the clipboard.
- **Auto-save** of your inputs to `localStorage`, so a reload keeps your numbers.
- **Light/dark theme** toggle (remembered separately from the numbers).
- **Mobile-first and accessible** — large touch targets, `inputmode` for numeric keyboards, live-region announcements, visible focus states, screen-reader-only labels.

## Default values

| Field | Default |
| --- | --- |
| Plan cost | $49.60 / month |
| Contract duration | 24 months (12 / 24 / 36) |
| Phone retail price | $2199 |
| Phone selling price | $2199 |
| Phone contract price | $1440 |
| Cost per sub-line | $17.06 / month |
| Number of sub-lines | 3 (max 3) |
| Annual rebate | $300 / year |
| Rebate applies to main line only | off |

## The maths

Let `people = subLines + 1` (the main line is a person too).

```
serviceTotal   = (planCost + subLineCost × subLines) × months
rebateTotal    = annualRebate × (months / 12)
phoneNet       = contractPrice − sellingPrice      // negative = profit
totalCost      = serviceTotal − rebateTotal + phoneNet

perPersonTotal = totalCost / people
perMonth       = perPersonTotal / months
```

When **"rebate applies to the main line only"** is on, service and phone costs are still split evenly and the whole rebate comes off the main line's share:

```
equalShare     = (serviceTotal + phoneNet) / people
mainLine       = equalShare − rebateTotal
eachSubLine    = equalShare
```

The group total is unchanged — but the main line can end up paying *less than nothing* if the rebate exceeds its share (shown as a negative amount, e.g. `−$37.50`).

The **retail price** is reference only. It illustrates the value of the deal and is never part of the split; the split uses what was actually paid versus what the phone actually sold for.

## Share link parameters

Inputs travel in the query string, so a link reproduces the exact scenario:

| Param | Field |
| --- | --- |
| `p` | Plan cost per month |
| `n` | Months (12, 24 or 36) |
| `r` | Phone retail price |
| `s` | Phone selling price |
| `c` | Phone contract price |
| `l` | Cost per sub-line |
| `m` | Number of sub-lines (0–3) |
| `a` | Annual rebate |
| `ro` | `1` when the rebate applies to the main line only |

Incoming values are sanitised: non-numeric input, out-of-range numbers and disallowed durations fall back to defaults.

## Notes

- Everything runs client-side. Nothing is uploaded anywhere.
- `localStorage` keys: `mobile-plan-split:v1` (inputs) and `mobile-plan-split:theme` (theme). Storage failures (private mode, blocked storage) are caught and ignored.
- The theme is opt-in via the toggle; the OS `prefers-color-scheme` setting is deliberately ignored, and the saved theme is applied before first paint to avoid a flash.

## Project structure

```
mobile-plan-calculator/
├── index.html   # markup, styles and logic — the whole app
└── README.md
```

## License

No licence specified.