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

Update (Day 26): Dynamic Trailing Stop-Loss
To maximize profitability during strong market trends, the static "Take-Profit" ceiling was replaced with an adaptive Trailing Stop-Loss system. This allows the engine to capture maximum upside momentum while strictly defining downside risk.
Dynamic Risk Logic
Peak Memory Tracking: Upon entering a position, the bot actively tracks and updates the highest_price achieved during the trade's lifespan.
Drawdown Execution: The system calculates the real-time drawdown from the recorded peak. If the price drops by 1.5% from the absolute high, the bot triggers a TRAILING_STOP execution to lock in profits.
Hard Stop Preservation: A static -1.0% Stop-Loss from the initial buy price remains actively calculated to protect capital in the event of an immediate reversal.
Technical Implementation
Real-time percentage delta calculations integrated directly into the core while loop.
Automated variable resets (highest_price = 0) tied to the "Cooldown" state to prevent memory leaks or logic errors between separate trade events.
Upgraded terminal UI to display real-time tracking of the active peak and current trailing drawdown percentage.

