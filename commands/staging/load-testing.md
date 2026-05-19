# 🔀 Command: load-testing

> Generates and runs load test scenarios.

## Instructions for Claude

1. Read `requirements.md` for performance targets
2. Generate load test script (k6, Artillery, or similar) with scenarios:
   - **Baseline:** 10 concurrent users for 2 minutes
   - **Normal load:** Expected concurrent users for 5 minutes
   - **Peak load:** 2x expected for 5 minutes
   - **Spike:** Sudden 10x burst for 30 seconds
3. Test critical endpoints:
   - Homepage / landing
   - Login flow
   - Core feature (read-heavy)
   - Core feature (write-heavy)
4. Measure: response time (p50, p95, p99), error rate, throughput
5. Compare against targets from requirements.md
6. Generate report with bottleneck analysis
