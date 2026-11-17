[![make.fun bounty](https://img.shields.io/badge/make.fun-bounty%20entry-6f42c1)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)

# GTA VI launch-day Twitch forecast

An entry for [make.fun bounty: Forecast GTA VI's launch-day Twitch viewers hour by hour](https://make.fun/bounty/gta6-twitch-ccu-forecast-01).

The program reads one case file (`/input/<id>.in`) holding everything the house
recorded about the "Grand Theft Auto VI" Twitch category up to the end of 2026-11-19
00:00-01:00 UTC, and writes five quantiles (5%, 25%, 50%, 75%, 95%) of the viewer
count for each of the day's remaining 23 hours to `/output/<id>.out`.

## Method

The forecast is built from the comparable launches the case carries, never from the
network: the program runs offline in a sandbox.

1. Each comparable launch's release-day curve is turned into log ratios to its own
   first hour, so launches of very different size become comparable.
2. Comparables are kept only when their day still ramps after the first hour (their
   peak lands in the afternoon or later), which separates a rolling-midnight launch
   from one already at its peak when the day opens.
3. Per hour, the centre is a kernel-weighted median of those ratios, weighted by how
   close the comparable's first hour is to this case's, in log scale: a launch of a
   similar size pulls hardest.
4. The spread is the robust dispersion (1.4826 x MAD) of the same ratios, floored, and
   widened by a factor calibrated by leaving one launch out at a time.

Quantiles are the centre plus the normal score of each level times the spread, in log
space, so a miss of 20% costs the same at 10,000 viewers as at 1,000,000.

Against the house baseline on the comparable launches, the leave-one-out mean pinball
loss is about 0.24 against the baseline's 0.34.

## Run it

```bash
mkdir -p out
MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

`main.py` is the packed entry the grader runs (Python 3.13, standard library only,
under a second per case, deterministic: the same case twice gives the same bytes).

## License

MIT, see [LICENSE](LICENSE).
