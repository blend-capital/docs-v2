# Lending

### How Does Lending with Blend Work?

Lenders supply assets they wish to lend to Blend lending pools. Borrowers then may borrow the assets by posting collateral to the pool and paying interest to lenders.

### How Do You Lend Using Blend?

Lenders provide assets to the lending pool and receive interest in return. Borrowers can borrow these assets by posting collateral and paying interest at loan repayment.

Lenders can withdraw lent assets from the protocol at any time as long as two conditions are met:

* The lending pool tokens are lent to must have sufficient liquidity — this means the pool must have more unborrowed assets than the amount the lender wishes to withdraw.&#x20;
  * If the pool does not have sufficient liquidity, the interest rate model will ensure liquidity increases. Blend's interest rate model raises interest if an asset is overutilized, ensuring that either borrowers repay, are liquidated, or more lenders enter the pool to receive the high interest rates. To learn more, see the [interest rate section](../../blend-whitepaper.md#interest-rates) of the whitepaper.
* The lender must be in good standing with the pool post-withdrawal — lenders have the option of using lent funds as collateral to borrow other pool assets. If they choose to do so, they are only allowed to withdraw lent funds if the withdrawal does not cause them to exceed their [borrow limit](borrowing.md#how-much-can-users-borrow). Otherwise, they must repay the borrowed funds before they can withdraw.

### Why would users lend on Blend?

In exchange for lending using Blend lending pools, lenders receive interest from borrowers and BLND issuance from the protocol (if their pool is in the reward zone and is allocating emissions to lenders of the asset the user is lending). In addition, when lending on Blend, users retain control of their funds. The protocol is decentralized, trust-free, and non-custodial. Only the protocol smart contracts have control over user funds, and users can withdraw their funds at any time.

### What is the interest rate for lending on Blend?

Lending interest rates equal the _borrowing interest rate_ multiplied by the _utilization ratio_ since interest paid by borrowers is distributed proportionally to lenders. Borrowing interest rates are demand-based and calculated using the utilization ratio. This means borrowing interest rates increase as the percentage of protocol assets lent to borrowers increases. See the [interest rate section](../../blend-whitepaper.md#interest-rates) of the whitepaper to learn more about how they are calculated.

### How do lenders receive interest?

When lenders withdraw their lent assets, they also withdraw any interest they have earned.

### Who controls my lent assets?

You do! Assets lent to the protocol are controlled by Blend's smart contracts. Therefore, you can use those smart contracts to withdraw your lent assets at any time.

### What assets can be lent?

Any Stellar-based asset supported by the lending pool you're using can be lent with Blend. If an asset isn't supported a new pool can be created to support it as long as sufficient backstop module depositors will support it.

### How do lenders withdraw lent assets?

Lenders can withdraw assets by using Blend's smart contracts. Doing so will also withdraw all interest the lender has accrued.

### Is there any situation where lenders can't withdraw assets?

If an assets utilization ratio is too high, there may not be sufficient liquidity for a lender to withdraw assets because too much of the asset balance is currently lent out. However, due to Blend's demand-based interest rate model, this is extremely unlikely. Interest rates are typically extremely high (over 100%) at high utilization ratios, which heavily incentivizes borrowers to repay their loans and new entities to lend to the protocol, thus adding liquidity and lowering the utilization ratio.

### Do Lenders Earn BLND Emissions?

Lenders earn BLND emissions if their pool is in the reward zone and the pool creator allocated emissions to lenders of the asset the user is lending. They may claim these emissions at any time at which point.
