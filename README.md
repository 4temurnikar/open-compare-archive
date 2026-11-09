# OpenCompare energy price history

A day-by-day record of every energy plan published under the Consumer Data Right (AER feed), kept
by the OpenCompare daily job. Written by `pipeline/energy/archive.mjs`; don't edit by hand.

- `events/YYYY/YYYY-MM-DD.jsonl`: one line per change on that day (Melbourne date):
  - `new`: first day the plan was recorded with prices (`p` = prices, `m` = name, type, area)
  - `price`: prices changed (`p` = new prices; `from` = previous plan ID if the version changed;
    `q: "unconfirmed"` = the retailer didn't update the plan's lastUpdated date, so worth checking)
  - `ver`: new plan version, prices unchanged
  - `meta`: name, type, customers or areas changed
  - `gone`: withdrawn (missing from two complete reads of the retailer's feed in a row; `last` = last day seen)
  - `back`: relisted after being withdrawn (`p` only if prices differ)
  - `rebase`: prices re-recorded after a change to how prices are compared (not a market change)
- `k` is the plan's series key: its plan ID without the version suffix (AGL1059355MBE4 → AGL1059355MBE).
- Prices (`p`) are in dollars excluding GST, as published: `e` electricity and `g` gas, each with
  tariff periods (`tp`), controlled load (`cl`), feed-in tariffs (`fit`), discounts (`disc`) and fees.
- `state.json`: where every plan stands now (for the next day's comparison); `state.prev.json` the day before.
- `runs/YYYY-MM-DD.json`: what each day's run saw and recorded.

Every run replays the whole log and refuses to write if the result doesn't match the state exactly.
