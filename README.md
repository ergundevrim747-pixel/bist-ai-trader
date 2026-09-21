# BIST AI Trader V3.3 Research Edition

V3.3 is a stricter, research-driven update of the BIST AI Trader technical engine. The goal is not to promise a high win rate; the goal is to reduce avoidable errors by requiring data quality, trend alignment, market confirmation, relative strength, volume/volatility confirmation, structure-based risk and a minimum reward/risk before an AL plan can pass.

## What changed in the research engine

- Multi-lens model instead of a single indicator: RSI, MACD, EMA20/50/200, momentum, volume, ADX/DI, OBV slope, candle close location, Bollinger/ATR contraction, support/resistance, price gap and extension.
- Higher-timeframe stage filter using a weekly 30-week moving average approximation (Stage 1/2/3/4). A Stage 4 market is treated as a risk regime rather than a normal long-entry environment.
- BIST 100 (XU100) market-regime filter and 60-session relative-strength comparison. Benchmark data is aligned by timestamp/floor date so historical decisions do not use future benchmark observations.
- Breakout quality filter: resistance break + strong close + volume expansion + trend strength + OBV confirmation. A fresh breakout may pass before ADX fully matures when price, volume and OBV are unusually strong, avoiding a known lagging-indicator problem.
- Pullback quality filter: established uptrend + proximity to EMA20/support + bullish candle + momentum/ADX confirmation.
- Overextension/gap filter: avoids chasing large extensions and unusually large gaps. Volatility-shock days are also blocked when short-term ATR expands sharply versus longer ATR.
- Entry-zone check: an AL plan can be rejected when the current/next-bar price is too far above the planned entry area.
- Stop logic combines recent swing/structure levels with ATR volatility. Plans wider than 7.5% or narrower than 0.8% are rejected by the safety gate. The trailing stop blends an EMA20 volatility trail with a 22-session high-water/Chandelier-style component and never moves downward.
- Target logic uses a minimum 2.0R first target and 2.8R second target, while checking historical resistance room for both targets. The app does not force a trade when the chart cannot support the required reward/risk.
- Position sizing is a model convention of 0.5% of the 100,000 TL paper account per initial risk, with a 25% notional cap. This is not a personalized recommendation.
- After Target 1, the backtest models a 50% partial exit, moves the remaining stop above breakeven, then uses a trailing stop; Target 2 closes the remainder.
- Conservative backtest: the entry candle is managed, stop has priority when stop and target are both touched, gap-through stops are filled at the opening price, and a small 0.05%/side slippage assumption is added on top of 0.20% commission.
- Benchmark-aware backtests use XU100 history and avoid look-ahead in benchmark regime calculations.
- Quote refresh no longer wipes a valid technical analysis on every intraday quote update. The technical model uses the latest complete daily OHLCV bar; quote refresh and technical-history refresh are separate concepts.
- Deep analysis refuses reconstructed OHLC. The İş Yatırım historical fallback is treated as close/volume-only for display and cannot create a deep stop/target plan.

## Research basis synthesized into the rules

The engine combines documented ideas from several established approaches rather than treating one trader or indicator as a universal solution:

- Long-horizon trend-following / momentum evidence: AQR documents persistent time-series momentum and the classic discipline of cutting losses and letting winners run.
- Momentum research: the CFA Institute's 2025 review describes momentum as persistent across eras/geographies while noting implementation and risk have to be managed carefully.
- Technical-analysis basics: Fidelity discusses RSI, MACD, Bollinger Bands, volume and support/resistance; Schwab discusses combining trend strength (ADX) with RSI and using volume to confirm price moves.
- Weinstein-style stage analysis: weekly 30-week trend/stage classification is used as a higher-timeframe filter, not as a standalone buy signal.
- Elder-style multi-timeframe logic: higher-timeframe direction is used to restrict lower-timeframe entries rather than trading every oscillator reversal.
- O'Neil/IBD-style loss discipline and distribution awareness: high-volume down days under weakening trend conditions are flagged as distribution, and the long gate blocks new entries during that condition rather than averaging down into pressure.
- Minervini/SEPA-style risk discipline: explicit invalidation, minimum reward/risk, tight setup quality and partial profit management are treated as gate conditions. The stop is not a one-size-fits-all percentage; it adapts to the chart and ATR.

These ideas are not assumed to have identical performance in BIST. The app therefore includes backtesting instead of presenting any rule as guaranteed. The engine is deterministic rules-based analysis; it does not pretend to “learn” continuously from the internet or from unverified trader claims. New rule changes are only admitted when they can be expressed mechanically and tested without look-ahead.

## Important data limitation

Default live quote source: public İş Yatırım quote pages. Public pages can be delayed, rate-limited or changed without notice. The app does not claim licensed real-time BIST market data.

Historical technical data: Yahoo Finance daily OHLCV is preferred when complete. If the public İş Yatırım historical endpoint is used as fallback and only close/volume is available, the app will show the history but refuse to generate deep stop/target calculations from fabricated OHLC.

BIST 100/XU100 is used as the broad market benchmark. Borsa İstanbul's own market documentation also describes circuit-breaker thresholds by market segment, so the engine treats extreme daily moves as a reason to be cautious rather than assuming normal liquidity.

## V3.2/V3.3 app behavior retained

- Automatic İş Yatırım stock-catalog discovery; no dependency on a permanently hand-written universe.
- Company name under ticker in detail (for example: THYAO / TÜRK HAVA YOLLARI).
- BIST 30 / 50 / 100 / 500 groups with periodic re-sync.
- Whole-catalog search.
- Refresh settings: 1 / 2 / 3 / 5 / 10 / 15 / 20 / 25 / 30 / 45 / 60 / 120 / 300 seconds.
- Home data status: source, last fetch, refresh interval, catalog size and market-filter status.
- Back navigation returns to the screen from which a stock was opened.
- Timestamp cache-buster on public requests.
- Virtual portfolio, alerts, signal history, comparison, daily candlestick/line chart.

## Build

GitHub Actions builds a debug APK with JDK 17, Android SDK 35 and Gradle 8.9. The repository workflow is self-contained: upload this ZIP plus the supplied workflow YAML to the repository root, then run the workflow.


## V3.5 — Self-Learning Engine
- Persistent signal outcome samples with bounded storage.
- Automatic harvesting during AI historical scans.
- Stop/Target-1 outcome labeling with conservative same-candle handling.
- Feature-level evidence for RSI, MACD, EMA, volume, trend, support and momentum.
- Bounded adaptive weights (0.75–1.25), shadow validation and activation gate.
- Learning dashboard and reset control in Settings.
- Adaptive weights influence confidence, while the 7-condition explanation remains transparent.
- The engine never claims guaranteed prediction and does not silently rewrite strategy rules without passing the validation gate.


## V3.5 — Sürekli sanal tahmin ve kendini denetleyen öğrenme
- Uygulama gerçek işlem yapmadan AL kurulumlarını "sanal tahmin" olarak kaydeder.
- Her tahminin giriş, stop, hedef 1/2, indikatör koşulları, trend/aşama, BIST100 rejimi, hacim, ATR, ADX ve göreli güç snapshot'ı saklanır.
- Yeni günlük OHLCV geldikçe tahminler otomatik değerlendirilir. Aynı günlük mumda stop ve hedef birlikte görülürse belirsiz sıra nedeniyle stop tarafı muhafazakâr biçimde önce sayılır.
- T1 sonrası kalan yarım pozisyon için başa baş koruma; T2 veya süre dolumu da etiketlenir.
- Kaybedilen tahminlerde volatilite şoku, piyasa koşulu, fake breakout, hacim teyidi, göreli güç, ADX, trend, aşırı uzama, dağıtım/satış baskısı gibi nedenler sınıflandırılır.
- Sonuçlar `prediction_samples` olarak öğrenme motoruna aktarılır; mevcut ağırlıklar bounded/safety-gated kalır.
- WorkManager arka planda en erken 15 dakikada bir derin piyasa taraması planlar. Bu, Android'in periyodik arka plan çalışma sınırı nedeniyle 1–5 saniyelik garanti vermez.
- Uygulama açıkken seçilen fiyat yenileme aralığı ayrı çalışır.
- Sistem broker emri vermez; yalnızca sanal/tahmin performansını izler. Gerçek zamanlılığın kalitesi veri kaynağına bağlıdır.
