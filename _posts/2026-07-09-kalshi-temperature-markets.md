---
layout: post
title: "Trading Chicago temperature on Kalshi: notes from small size"
tag: "prediction markets"
summary: "What daily-high temperature contracts taught me about binary payoffs, information lag, and why my worst trade was the one I was most confident in."
reading_time: "7 min"
---

Last December I traded Kalshi's Chicago daily-high temperature markets with live money. The size was deliberately small — the point was to learn the mechanics of binary event markets with real skin in the game, not to make rent. This is a record of what the market structure looks like from the inside, where I think the edge was, and where I lost money being sure of myself.

## The contract

Kalshi's temperature markets are binary contracts on the day's high at an official weather station, bucketed into ranges (e.g. "37° to 38°"). Settlement is against the NWS daily climate report. Price is an implied probability: a bucket trading at 30¢ is the market saying 30% that the high lands in that range.

Two structural features matter more than anything else:

1. **The underlying resolves gradually in public view.** Unlike an election or an earnings print, the day's high is being *observed* in real time. Hourly observations are public. By early afternoon, the distribution of possible outcomes has collapsed to one or two buckets. The contract is a decaying-uncertainty instrument, closer to an expiring option than a coin flip.
2. **Buckets are narrow and adjacent.** One degree of error moves you from a full payout to zero. There is no partial credit. This punishes point forecasts and rewards thinking in distributions.

## Where the edge was

The trades that worked were not weather forecasting. They were latency arbitrage against human attention: the official observations update on a schedule, and the order book reprices slower than the information arrives — especially in the hours when the high is "locking in." If the 2pm observation makes the 37–38° bucket near-certain and the book is still pricing it in the 70s, that gap is the trade. No model, just watching the data source the contract settles on, faster than the marginal participant.

Two of these worked: adjacent-day Chicago highs settled Yes for +$12.27 and +$11.39. Small numbers; the mechanism is the interesting part. This is the same trade as any stale-quote strategy — the "quote" here is just a probability that lags a public data feed measured in minutes, which tells you something about how early this market still is.

## Where I lost money

The instructive trade is the one that went -$75.00 — my largest position of the batch, on the bucket I was most confident about. The high came in outside my range. Total loss, by construction.

The post-mortem is not "the forecast was wrong." Forecasts are allowed to be wrong. The errors were:

1. **I sized a distribution problem like a point-estimate problem.** I had a number in my head for the high and bought the bucket containing it, instead of pricing the neighboring buckets and asking whether the market's *distribution* disagreed with mine. On a one-degree-wide bucket, forecast dispersion of even ±1.5° means the "obvious" bucket is rarely worth its price.
2. **Position sizing was inverted.** My largest position was the trade with the least informational advantage — entered early, before observations had constrained anything, when I was betting on a forecast like everyone else. The winners were small positions taken late, when I actually had an information-lag edge. Size should have followed edge; instead it followed conviction.

I also paid tuition in two markets I had no business touching: a jobs-number contract (-$1.95) and an NBA championship market (-$20.02). No informational edge, no model, pure entertainment priced accordingly. I classify these honestly as the cost of learning what *not having an edge* feels like from the inside — it feels exactly like having one, which is the problem.

## What systematizing this would look like

The discretionary version doesn't scale past pocket money, but the structure of a real system is visible from here:

- **Data**: NWS hourly observations plus forecast ensembles (HRRR/NBM), aligned to the settlement station and the report's measurement window.
- **Model**: price the full daily-high distribution, not a point — ensemble spread maps directly to bucket probabilities. Compare model distribution to the book's implied distribution; trade the divergence.
- **Execution**: the books are thin. Edge measured mid-to-mid overstates what's capturable; realistic sizing is bounded by displayed depth, and crossing the spread eats a large fraction of small edges.
- **Sizing**: fractional Kelly on the model-vs-market probability gap, with a hard cap per market — the -$75 rule, institutionalized.

The general lesson transfers beyond weather: in event markets, the valuable question is rarely "what will happen?" It's "what does the settlement source say, when does it update, and who is watching it slower than I am?"

*Positions and P&L above are my own, from a personal account, at sizes best described as academic.*
