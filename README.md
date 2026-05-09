Current Strategy: Dual SMA Crossover
The engine currently monitors RELIANCE.NS using a 1-minute interval.
Fast Signal:45-Period Simple Moving Average ($SMA_{45}$).
Slow Signal: 190-Period Simple Moving Average ($SMA_{190}$).
Logic:BUYwhen $SMA_{45}$ crosses above $SMA_{190}$.
SELL when $SMA_{45}$ crosses below $SMA_{190}$.

Risk Management (Day 24 Update)
To prevent drawdown during high volatility, the bot features a Dynamic Risk Tripwire:
Stop-Loss: Automatically exits positions if the price drops by 1%.   
Take-Profit: Automatically secures gains if the price rises by 2%.
Cooldown Mode: Prevents "revenge trading" by pausing the bot until a natural trend reset occurs after an emergency exit.

Technical Features
State Persistence: Recovers the last known trade position from `trade_log.csv` if the system restarts.
Real-time Notifications: Sends instant trade alerts via SMTP (Gmail) with PnL details.
Latency Optimized: Minimal data payloads to ensure fast signal processing.
Performance Tracking: Logs every trade with a timestamp, price, and realized PnL.

Tech Stack
Language:Python 3.x
Data Source `yfinance` API
Notification: `smtplib` (SMTP_SSL)
Data Analysis: `pandas`, `numpy`

(Day 25): RSI Momentum FilterThe trading engine has been upgraded from a simple trend-following script to a multi-condition algorithmic system. 
To combat "whipsaw" losses in choppy markets, a Relative Strength Index (RSI) filter was built from scratch and integrated into the execution logic.
Upgraded Strategy: Trend + ExhaustionThe bot now requires two independent conditions to align before risking capital:Trend Confirmation: The fast $SMA_{45}$ must cross above the slow $SMA_{190}$.Momentum Exhaustion: The 14-period RSI must be below 60.This ensures the engine does not execute a "BUY" on an asset that is already overbought, protecting against sudden price corrections at the top of a spike.

The Custom Math EngineInstead of relying on external indicator libraries, the RSI logic is hardcoded using pure pandas for maximum execution speed and deep customization:Uses .diff() to calculate minute-by-minute price velocity.Utilizes .clip() to isolate positive gains and negative losses into independent rolling matrices.Applies a 14-period .rolling().mean() to calculate the Relative Strength (RS) ratio, normalized into a real-time 0-100 RSI score.
