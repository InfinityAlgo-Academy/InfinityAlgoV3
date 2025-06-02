# InfinityAlgoV3
**Professional Description:**

InfinityAlgo V3 is a comprehensive trading indicator for the TradingView platform, engineered to provide traders with clear buy and sell signals based on a proprietary trend-following algorithm. The core of its signal generation relies on an Average True Range (ATR) calculation, with user-adjustable sensitivity to fine-tune performance across different market conditions and timeframes.

The indicator enhances chart analysis through:
*   **Visual Buy/Sell Signals:** Clearly plotted labels on the chart for entry points.
*   **MACD-Based Candle Coloring:** Dynamically colors candles to reflect trend strength and direction, utilizing MACD and its histogram. This provides an at-a-glance understanding of market momentum.
*   **Configurable Risk Management Inputs:** Includes parameters for setting take profit and stop loss levels, allowing traders to define their risk-reward ratios.
*   **Smart Trail:** Implements an intelligent ATR-based trailing stop loss. This feature dynamically adjusts the stop loss level as the price moves favorably, aiming to protect profits while allowing trades room to mature. It incorporates Fibonacci levels for potential exit or partial profit-taking zones.
*   **Customizable Tools:** Offers toggles for features like a "Reversal Cloud" and a "Trend Following" mode, providing additional analytical perspectives (though their specific logic is not fully detailed in the provided code excerpt).
*   **Alert System:** Generates alerts for buy signals, sell signals, and changes in market direction, enabling traders to stay informed of potential opportunities even when not actively monitoring charts.

**How It Works:**

1.  **Core Signal Generation:**
    *   The script calculates `Periods` based on the user-defined `sensitivity`.
    *   It uses `hl2` (the midpoint of the high and low) as the source price (`src`).
    *   An Average True Range (`atr`) is calculated over these `Periods`.
    *   Two dynamic bands, `up` (potential resistance/sell entry) and `dn` (potential support/buy entry), are calculated using the `src`, a `Multiplier` (derived from `sensitivity`), and the `atr`.
    *   These `up` and `dn` bands are adaptive:
        *   The `up` band can only move higher or stay the same if the previous close was above the previous `up` band value.
        *   The `dn` band can only move lower or stay the same if the previous close was below the previous `dn` band value.
    *   A `trend` variable tracks the current market direction (1 for uptrend, -1 for downtrend).
        *   The trend flips to `1` (uptrend) if it was previously `-1` and the `close` price crosses above the `dn1` (previous `dn` band) value.
        *   The trend flips to `-1` (downtrend) if it was previously `1` and the `close` price crosses below the `up1` (previous `up` band) value.
    *   A `buySignal` is generated when the `trend` changes from -1 to 1.
    *   A `sellSignal` is generated when the `trend` changes from 1 to -1.
    *   These signals are plotted on the chart if `showsignals` is enabled.

2.  **Candle Coloring:**
    *   The script calculates MACD (Moving Average Convergence Divergence) values: `macd` line, `signal` line, and `hist` (histogram).
    *   Candle bodies are colored based on the MACD and histogram conditions:
        *   **Ranging/Weak Trend:**
            *   If `hist` is positive and increasing: `greenLow` (light green/blue in this config).
            *   If `hist` is negative and decreasing: `redLow` (light red/purple in this config).
        *   **Bullish Trend:**
            *   If `macd` is positive and `hist` is positive: `greenMidLow`.
            *   If bullish momentum increases (e.g., `hist` increasing over prior bars while `macd` remains positive): colors shift to `greenMidHigh` and `greenHigh`.
        *   **Bearish Trend:**
            *   If `macd` is negative and `hist` is negative: `redMidLow`.
            *   If bearish momentum increases: colors shift to `redMidHigh` and `redHigh`.
        *   A default color (`yellowLow`) is used if none of the specific trend conditions are met.

3.  **Smart Trail (Trailing Stop Loss):**
    *   It calculates a `trueRange` (either standard or a modified version based on `trailType`).
    *   A `loss` value is determined by multiplying an ATRFactor with the Wilder's Moving Average of the `trueRange`.
    *   Initial trailing stop levels (`Up` for longs, `Dn` for shorts) are set based on the current `close` minus/plus the `loss`.
    *   Similar to the main signal logic, `TrendUp` and `TrendDown` lines are established. `TrendUp` only moves up or stays flat, and `TrendDown` only moves down or stays flat, creating a trailing effect.
    *   The `trail` line (the actual stop loss) follows `TrendUp` in an uptrend and `TrendDown` in a downtrend.
    *   The script tracks the `ex` (extreme price – highest high in an uptrend, lowest low in a downtrend) since the last trend change.
    *   Fibonacci retracement levels (61.8%, 78.6%, 88.6%) are calculated between this `ex` price and the current `trail` level.
    *   If `ShowSmartTrail` is enabled, the smoothed `trail` line and the 78.6% Fibonacci level (`f2`) are plotted, with the area between them filled to visually represent the trailing stop zone.

4.  **Alerts:**
    *   `alertcondition` is used to create alerts for:
        *   `buySignal`: When a buy signal occurs.
        *   `sellSignal`: When a sell signal occurs.
        *   `changeCond`: When the `trend` variable changes, indicating a potential shift in market direction.

The other input options (`levels` for take profit/stop loss, `show_rev` for Reversal Cloud, `TrendFollower`) control features whose detailed logic is not fully present in the provided excerpt but would integrate into the overall trading system provided by the indicator.
