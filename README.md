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

Update (Day 27): Connection Resilience & System Logging
The focus of this update was to transition the engine from a local script to a resilient, "always-on" autonomous system. The architecture was refactored to handle the volatility of internet connectivity and API stability.
Resilience Features
Recursive Retry Logic: The data-fetching module is now wrapped in a persistent loop. In the event of a network failure or empty API response, the bot initiates a 30-second "cool-off" before retrying, preventing the application from crashing.
Global Exception Handling: Implemented a top-level "Safety Net" that catches unhandled exceptions, logs the error trace, notifies the user via email, and automatically restarts the trading cycle.
Autonomous Logging Engine: Added a secondary logging system (system_log.txt) to track internal health metrics, connectivity issues, and system resets, separate from the trade execution logs.
Technical Implementation
Optimized the while True main loop for 24/7 execution.
Integrated try-except-finally blocks to ensure system memory (like the peak price tracker) is preserved even during minor errors.
Engineered a "Self-Healing" mechanism to ensure 99.9% uptime during market hours.

v1.5 Update (Day 28): Command Center & Performance AnalyticsThis update transforms the terminal output into a high-visibility dashboard, providing real-time quantitative metrics and visual cues for better monitoring.

Analytics & UI FeaturesSession Persistence: On startup, the engine parses trade_log.csv to reconstruct historical performance data, ensuring that Total PnL and Win/Loss metrics are preserved across restarts.
ANSI Color Mapping: Integrated a color-coded notification system to provide immediate status awareness:Green: Entry signals and profitable trade windows.Red: Exit signals, Stop-Loss triggers, and drawdowns.Yellow: Overbought/Oversold RSI alerts and connection warnings.
Live Dashboard Ticker: Refactored the logging output into a non-scrolling "Live Ticker" that updates the current price, RSI, active trade PnL, and total account health on a single line.
Technical ImplementationWin Rate Formula: $Win Rate = \left( \frac{Total Wins}{Total Trades} \right) \times 100$Utilized sys.stdout.write and \r for in-place terminal updates.Implemented automated PnL aggregation using pandas for historical log recovery


(Day 29): Incremental Data Processing
Refactored the data ingestion engine to move away from redundant bulk downloads, significantly optimizing network bandwidth and processing speed.

Optimization Logic
Stateful Data Management: The bot now maintains a master_df in active memory, eliminating the need to re-calculate historical indicators from scratch every minute.

Sliding Window Architecture: Implemented a buffer limit of 250 rows. As new 1-minute candles are "stitched" to the bottom of the DataFrame, the oldest data points are "trimmed" from the top.

Duplicate Mitigation: Integrated a deduplication layer to handle overlapping data points from the API provider, ensuring the SMA and RSI calculations remain mathematically precise.

Day 30- Multi-Factor ConfluenceThis update marks the completion of the first month of development. The engine has evolved from a basic "if-then" script into a sophisticated execution model utilizing Triple-Factor Confirmation.
Advanced Decision LogicThe bot now requires a "Green Light" from three independent mathematical perspectives before committing capital:Trend Detection: Utilizing a $45/190$ SMA crossover to identify macro-bullish environments.Mean Reversion: Utilizing RSI to identify local price pullbacks (buying the dip).Momentum Confirmation: Integrated MACD (Moving Average Convergence Divergence) to ensure price action has reversed upward before entry.Technical Formulae Implemented:MACD Line: $EMA_{12} - EMA_{26}$Signal Line: $9-period\ EMA\ of\ MACD$Trigger: Entry only occurs when $MACD_{Line} > Signal_{Line}$ System FeaturesLive Dashboard: Real-time ANSI-colored terminal UI showing PnL, Win/Loss ratios, and technical indicators.Stateful Memory: Incremental data processing via a sliding window (250-minute buffer) for $O(1)$ time-complexity updates.Resilience Layer: Full exception handling and recursive "retry" logic for 24/7 autonomous uptime.

Day 31: ATR-Driven Dynamic Volatility StopsBegan Month 2 of development by transitioning from static risk parameters to an adaptive volatility-aware framework.
Risk EngineeringAverage True Range (ATR) Integration: Implemented the ATR indicator to quantify real-time market noise.
This prevents "whipsawing"—where a bot is stopped out due to normal volatility rather than a trend reversal.
Dynamic Stop-Loss Logic: Replaced the fixed 1.0% stop-loss with a multiplier-based system.Formula: $Stop\ Loss = Entry\ Price - (ATR \times 2.0)$Adaptive Buffering: The system now automatically grants "room to breathe" for trades during high-volatility sessions while tightening protection during low-volatility consolidation.
Technical Stack Upgrades:Enhanced pandas vectorization for True Range calculation using .shift() and .abs() functions.Updated the Command Center UI to display the active Dynamic ATR Stop Price in real-time.

Day 32 - Dynamic Position Sizing Engine
Upgraded the execution engine to handle real-world capital allocation via risk-adjusted position sizing, based on real-time volatility metrics. The system no longer trades static quantities; it dynamically sizes every entry to normalize risk.
Capital Management Implementation
Fixed Fractional Risk: Implemented a global portfolio parameter, hard-capping the maximum allowable loss per trade to exactly 1% of the total rolling account equity.
Volatility-Adjusted Quantity: The bot calculates exact share order sizing dynamically using the current ATR (Average True Range) stop distance:
Position Size = Total Risk Capital / ATR Stop Distance
Compounding Ready: As the portfolio grows from profitable trades, the 1% risk allocation automatically scales up the position sizing for future trades, allowing for autonomous mathematical compounding.
UI Upgrade: The Command Center live ticker was overhauled to display real-time Qty (Shares Held), Acct (Total Portfolio Capital), and active monetary values (₹) rather than raw price-point differences.

Day 33: Mathematical Expectancy Engine (Dynamic Take-Profit)Upgraded the execution engine to enforce algorithmic profit extraction based on mathematical risk-to-reward ratios.
Asymmetric Risk Management ProfileDynamic Profit Targets: Implemented an automated profit extraction layer mapped to current market volatility.$\text{Take-Profit Target} = \text{Entry Price} + (ATR \times 3.0)$Enforced Risk-to-Reward (R:R): Combined with the existing $2.0 \times ATR$ stop-loss framework, the execution engine enforces an explicit 1:1.5 Asymmetric R:R structure.
State Management: Added take_profit_price state persistence within the primary control loop to monitor and immediately execute limit-style exits when targets are breached.UI Overhead: Expanded live telemetry to print the targeted monetization zone whenever a capital allocation position is active.


Day 34- State-Aware Breakeven EngineUpgraded the risk management framework to include dynamic state-tracking, effectively eliminating downside risk on trades that experience significant early momentum. Also patched the external SMTP notification engine for better network transparency.
Capital Preservation ImplementationAlgorithmic Breakeven Shift: Introduced the breakeven_active state Boolean. The system continuously calculates the live price delta and automatically triggers a state change when a position moves $1.0 \times ATR$ in a profitable direction.Dynamic Stop-Loss Override: Once the breakeven state is achieved, the primary volatility stop-loss is programmatically overridden and hard-pegged to the exact execution entry price.Risk-Free Expectancy: This mechanic creates a mathematically risk-free trade scenario ("Free Ride"), ensuring that deep-profit reversals result in a zero-loss PnL rather than a full 1% capital drawdown.Granular Exit Telemetry: Added a specific 'BREAKEVEN_STOP' trigger tag to the trade logs and email engine to distinguish between standard risk drawdowns and capital preservation exits.
Notification Engine PatchException Handling Overhaul: Removed silent pass cloaking from the smtplib execution block. Network, TLS/SSL, and authentication exceptions (like Google App Password blocks) are now explicitly printed to the terminal UI for live system debugging

(Day 35) - Position Scaling & Partial Liquidation Engine
Upgraded the execution engine from single-block exits to an asymmetric multi-stage liquidation system. The architecture now actively scales out of positions to lock in realized PnL while maintaining market exposure to capture extended macroeconomic runs.
Multi-Stage Position Management Implementation
State-Dependent Slicing: Implemented a new control state variable, partial_profit_taken, paired with a volatility trigger parameter PARTIAL_TP_MULTIPLIER = 1.5.
Dynamic Fractional Liquidation: When the active market price crosses the partial profit threshold, the system computes an sub-position split using floor division:
shares_to_sell = math.floor(current_shares / 2)
Portfolio Delta Management: The execution block seamlessly updates the global state by deducting the liquidated shares from current_shares, adding the realized fractional PnL directly to PORTFOLIO_CAPITAL, and allowing the residual position ("the runner") to remain active under the protection of the entry-level breakeven stop-loss.
Telemetry & File I/O Logging: Upgraded both the persistent trade_log.csv and the real-time SMTP alerting network to flag and track intermediate PARTIAL_TP actions independently from final structural trend or target exits.

 Milestone Update: v2.3 (Day 36) - Temporal Execution Guard & Anti-Churn EngineUpgraded the execution engine with a temporal blockade framework designed to mitigate account churn and excessive transactional overhead during high-frequency micro-consolidations (sideways markets).
Temporal Cooldown Architecture ImplementationEpoch Time Tracking: Integrated native epoch timestamping via time.time() to track trade expirations.
Added global persistent state container last_exit_time = 0 to preserve accurate historical exit timestamps.Asymmetric Entry Blockade: Wrapped the primary strategy execution engine in a conditional time-differential filter:$$\Delta t = \frac{\text{time.time()} - \text{last\_exit\_time}}{60}$$If $\Delta t < \text{COOLDOWN\_MINUTES}$ (parameterized at 15), the system completely restricts position entry vectors, bypassing technical indicator cross-overs until structural market telemetry resets.Multi-Exit Time Stamping: Refactored all localized risk exit modules (TAKE_PROFIT, BREAKEVEN_STOP, ATR_STOP_LOSS, TRAILING_STOP, and standard technical trend trend SELL closures) to dynamically record the system time upon order completion.Terminal UI State Machine Expansion: Overhauled the Command Center rendering block to parse live state machine behavior:Displaying a localized yellow countdown ticker 🔒 Lockout: [X.X]m left when the system is constrained by the temporal block.Reverting to a green 🟢 Scanning Markets state upon timer expiration and cooldown completion.
