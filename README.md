Current Strategy: Dual SMA Crossover
The engine currently monitors **RELIANCE.NS** using a 1-minute interval.
* **Fast Signal:** 45-Period Simple Moving Average ($SMA_{45}$).
* **Slow Signal:** 190-Period Simple Moving Average ($SMA_{190}$).
* **Logic:** * **BUY** when $SMA_{45}$ crosses above $SMA_{190}$.
    * **SELL** when $SMA_{45}$ crosses below $SMA_{190}$.

Risk Management (Day 24 Update)
To prevent drawdown during high volatility, the bot features a **Dynamic Risk Tripwire**:
* **Stop-Loss:** Automatically exits positions if the price drops by 1%.
* **Take-Profit:** Automatically secures gains if the price rises by 2%.
* **Cooldown Mode:** Prevents "revenge trading" by pausing the bot until a natural trend reset occurs after an emergency exit.

Technical Features
* **State Persistence:** Recovers the last known trade position from `trade_log.csv` if the system restarts.
* **Real-time Notifications:** Sends instant trade alerts via SMTP (Gmail) with PnL details.
* **Latency Optimized:** Minimal data payloads to ensure fast signal processing.
* **Performance Tracking:** Logs every trade with a timestamp, price, and realized PnL.

Tech Stack
* **Language:** Python 3.x
* **Data Source:** `yfinance` API
* **Notification:** `smtplib` (SMTP_SSL)
* **Data Analysis:** `pandas`, `numpy`
