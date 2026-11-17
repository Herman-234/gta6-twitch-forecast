# GTA6 Twitch Forecast — gta6-twitch-ccu-forecast-01

[![make.fun](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Herman-234/gta6-twitch-forecast/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Herman-234/gta6-twitch-forecast/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)

Entry for the make.fun bounty: https://make.fun/bounty/gta6-twitch-ccu-forecast-01

A forecaster for Grand Theft Auto VI's launch-day Twitch viewers, hour by hour
(`main.py`, Python 3.13, standard library only). It reads one case and writes,
for each of hours 2 to 24 of the release day (UTC), five quantiles of that
hour's concurrent-viewer count in the GTA VI Twitch category.

## Method

A comparable-launch ratio curve, fitted on the case's own comparables. Every
past big launch contributes its release-day shape as log-ratios to its hour-1
print; the forecast anchors on this case's hour-1 print and scales it by the
pool's per-hour median ratio. Three choices the data forced:

- **Pool: Tier "comparable" only.** Early-access-contaminated launches have
  an inflated hour 1, "weak" ones are free-to-play/slow-burn launches with a
  different shape, and the GTA VI target-history row is not a launch day.
- **Similarity weights:** console-only rolling-midnight launches count most
  (1.0), mixed PC/console rolling half (0.5), simultaneous-global a quarter
  (0.25). GTA VI unlocks at rolling local midnight on consoles.
- **Hour-1 conditioning:** across past launches, the bigger the hour-1 print,
  the less the day grows afterwards (evening log-ratio vs log hour-1
  correlates about -0.9). The median curve is shifted by half that fitted
  slope, measured against the pool's median hour-1 size -- half, not full,
  because GTA VI extrapolates past the 24-launch pool.

Spread: the weighted interquartile range of the pool's log-ratios at each
hour, as a log standard deviation, floored at 0.20 and capped at 0.90.
Quantiles are the median times exp(z * spread) at the five levels.

## Local validation (my own scoring of the kit's practice data)

| Test | This entry | House baseline |
| --- | --- | --- |
| Practice mean, 4 real cases (SullyGnome answers) | **0.2194** | 0.3871 |
| Leave-one-out, 24 comparable launches | **0.5490** | 0.8662 |

Practice cases, one by one (mine vs baseline): Wolverine 0.278 vs 0.744,
Red Dead Redemption 2 0.213 vs 0.287, God of War Ragnarök 0.172 vs 0.215,
Spider-Man 2 0.214 vs 0.303. Leave-one-out wins 21 of 24 launches.
90% coverage on practice 0.91–1.00 (an honest forecast covers about 0.9).
Runtime: 0.27 s for all four practice cases (limit 8 s per case);
two runs are byte-identical.

Honest caveat: the practice is past launches, not GTA VI itself. The final
depends on how big hour 1 actually is and on the US unlock wave; the spread
above is the honest statement of that uncertainty, and the entry only pays if
it beats the best house entry on the recorded day.

## Files

- `main.py` — the forecaster (entrypoint)
- `LICENSE` — MIT
