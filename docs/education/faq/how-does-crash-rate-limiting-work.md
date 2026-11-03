# How Does Crash Rate Limiting Work?

BugSplat enforces rate limits on crash reports to prevent rapid exhaustion of your monthly crash allocation in a short period of time. This policy ensures consistent usage of your crash reporting capacity over the duration of your subscription period, avoiding sudden spikes that could cause you to exceed your monthly limit unexpectedly.

**How Rate Limiting Works:** Rate limiting is designed to control how many crash reports can be sent to BugSplat over short periods. This protects your monthly crash allowance from being quickly depleted by sudden spikes in crashes.

To determine the rate limit, BugSplat first calculates the average number of crashes per minute that your subscription allows. We then set the rate limit at **10x** this average value. This allows flexibility for temporary increases in crash volume while maintaining protection against sustained high-frequency bursts that could exhaust your limit prematurely.

For example, if your plan allows for **100,000 crashes per month**, BugSplat calculates the average number of crashes per minute based on that allowance. The rate limit is then set at **10 times** the average, ensuring that brief spikes in crash reports can be accommodated without exceeding your monthly limit too quickly.

