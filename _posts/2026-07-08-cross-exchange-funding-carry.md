---
layout: post
title: "Cross-exchange crypto funding-rate carry: what survived the robustness tests"
tag: "strategy"
summary: "A delta-neutral carry strategy across six venues: 5.76% net CAGR, 1.22% max drawdown — and the single-exchange version loses money. Notes from the robustness section."
reading_time: "10 min"
---

This is a write-up of a delta-neutral funding-rate carry strategy built as my Quantitative Trading Strategies final project at UChicago, joint work with Cesare Bavaresco, Pietro Candiani, Thibaut Desauty, and Alan Donnelly. The project was split by section; I owned robustness and out-of-sample validation, which is why this post leans on that part. All numbers below are from our backtest (hourly data, Jan 2020 – Mar 2026) and come with the limitations listed at the end.

## The inefficiency

Perpetual futures charge a funding rate — periodic payments between longs and shorts that tether the perp to spot. The rate itself is not the trade; it's well known and mostly arbitraged on any single large venue. The trade is that **funding rates for the same asset diverge persistently across exchanges**, and the divergence is large: on our panel, the best-exchange funding rate for BTC averaged 1.28 bps per 8h against Binance's 0.07 bps.

Why doesn't this close? The boring reasons: capital has to sit pre-positioned on multiple venues, cross-exchange routing is operationally annoying, liquidity is fragmented, and most retail flow wants directional exposure, not basis. None of these are mysterious. All of them are real enough to leave carry on the table.

## Strategy construction

The structure is deliberately simple:

- **Universe**: 7 assets (BTC, ETH, SOL, BNB, XRP, DOGE, AVAX) × 6 exchanges (Binance, Gate, OKX, Bybit, Deribit, Hyperliquid), hourly funding since 2020, resampled to the 8h settlement cycle.
- **Signal**: z-score of the funding rate against a 90-day rolling window. Enter when |z| > 2.0, exit when z crosses 0. Short the perp on the best-funding exchange, hedge with spot on Binance. Delta-neutral by construction.
- **Sizing**: fixed 1/7 of capital per asset, no leverage, full collateral on both legs.
- **Gates**: block entries when open interest is below 0.5× its rolling mean (liquidity), and flatten on VIX > 30 or SPY 5-day drawdown > 5%.

Net of a 14.5 bps round-trip cost assumption: **5.76% CAGR, 3.95 daily Sharpe, 1.22% max drawdown, 0.71% annualized vol** over 525 trade cycles. Hit rate is 34% — the strategy loses more often than it wins, with a 4.06 profit factor doing the work. Read that combination carefully: it earns like a bond and occasionally collects a large funding dislocation.

## The result I find most interesting

The cross-exchange part is not an enhancement — it is the entire strategy. Restricted to Binance only, the same signal from 2023 onward produces **-10.0% CAGR with a 25.6% max drawdown**. Cross-exchange over the same window: +6.2% and 1.2%. Single-venue funding carry on the largest exchange is dead; the alpha lives in the routing.

The routing itself is concentrated: ~55% of position-periods ended up on Hyperliquid, whose 1-hour settlement (vs. 8h elsewhere) creates a structural premium. That's simultaneously the engine of the strategy and its biggest fragility — more below.

## Robustness (my section)

Eight tests, all pass, with the caveat that "pass" means "stable in backtest," not "guaranteed to work":

- **Lookback window** (180–540 periods): CAGR stable, 4.9–7.3%.
- **Entry threshold** (z from 1.0 to 2.5): 5.2–6.2% CAGR; lower thresholds trade more and earn slightly less per trade. The parameter matters less than I expected, which I read as the signal being coarse but real.
- **Signal persistence**: rank autocorrelation of 0.557 at one lag (8h), decaying to 0.226 at 7 days. The dislocations are not noise; they last long enough to trade at 8h rebalancing.
- **Weight scheme**: fixed 1/7 beats equal-investing in active positions — mostly by generating 4.7× less turnover, i.e. by paying less in fees, not by being smarter.
- **Universe sensitivity and leave-one-out**: dropping any single asset moves CAGR within 5.5–5.9%. No single-asset dependency.
- **Cost sweep**: still positive at 30 bps round-trip, 2.2× our baseline assumption. Break-even at 31.4 bps.
- **Out-of-sample**: parameters were set ex ante and the train/test split (2020–22 vs 2023–26) produced 5.6% vs 5.5% CAGR. I would resist over-reading the 99.8% consistency — the test period also happens to be when Hyperliquid came online, so the regime shifted in the strategy's favor for reasons unrelated to the signal.
- **Crisis episodes**: COVID, LUNA, FTX, SVB, Aug-24 vol spike — worst episode loss was 0.20% (FTX). Delta-neutrality did its one job.

## Limitations, stated plainly

The backtest assumes execution at hourly close prices with no slippage or partial fills; on thin perp books that flatters the results. The strategy would suppress the very rates it harvests at any institutional size — this is a capacity-constrained trade. Bear markets yield near-zero returns (2022: -0.3%), and while the max drawdown is 1.22% deep, its max *duration* was 439 days — a long time to explain to anyone why the strategy is flat. And the Hyperliquid concentration means a fee change or settlement redesign on one young exchange materially rewrites the P&L.

The honest one-line summary we converged on: crypto-grade alpha, bond-grade risk, savings-account-grade capacity.

*Code and full analysis were developed as coursework; figures above are from our final results. A cleaned public re-implementation of my sections is in progress.*
