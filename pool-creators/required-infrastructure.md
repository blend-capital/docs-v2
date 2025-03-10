---
description: >-
  There are multiple bots that pool creators need to ensure are running to
  maintain pool safety.
---

# Required Infrastructure

### Liquidation & Bad Debt Auction Bot

Liquidation bots must be set up to monitor the pool and liquidate any underwater users. The same bot should be used to fill bad debt auctions if one is created. Without one of these bots, users may create bad debt, harming backstop depositors.

Liquidation Auction Documentation: [liquidation-management.md](../tech-docs/core-contracts/lending-pool/liquidation-management.md "mention")

Bad Debt Auction Documentation: [bad-debt-management.md](../tech-docs/core-contracts/lending-pool/bad-debt-management.md "mention")

Example Liquidation & Bad Debt Auction Bot: [https://github.com/blend-capital/liquidation-bot](https://github.com/blend-capital/liquidation-bot)

### Interest Auction Bot

A bot must also be run to fill interest auctions.&#x20;

Interest Auction Documentation: [interest-management.md](../tech-docs/core-contracts/lending-pool/interest-management.md "mention")

Example Interest Auction Bot: [https://github.com/blend-capital/liquidation-bot](https://github.com/blend-capital/liquidation-bot)
