# Borrowing

### How does borrowing work on Blend?

Borrowing from Blend requires users to post sufficient collateral to the lending pool they are borrowing from. The collateral posted is also lent to the pool, so borrowers generate interest on it.\
\
The amount of collateral the borrower must post depends on their collateral's Collateral Factor(CF) and the borrowed asset's Liability Factor(LF). The required dollar value of their collateral can be calculated with the following formula:

$$CollateralValue = \frac{LiabilityValue}{LF*CF }$$

So if the user has collateral with a $$CF$$ of 0.5 and is attempting to borrow $450 of an asset with a $$LF$$of 0.9, they must post at least $1000 of collateral.

Borrowers must always maintain this collateral ratio as the value of their collateral and liabilities shifts. If they ever lack sufficient collateral, they will be [liquidated](liquidations.md).

### How do you borrow using Blend?

To borrow using Blend, borrowers must first select a lending pool to borrow from. When choosing a pool to borrow from, users should:

* Check the pool supports borrowing of the asset they want to borrow.
* Check that the pool supports collateralizing the asset they wish to collateralize.
* Check that the Collateral Factor and Liability Factors are sufficient for the level of debt the user wants to incur against their collateral.
* Ensure the pool has a well capitalized backstop module.
  * The backstop module is a fund of assets insuring the pool against bad debt, so well-capitalized backstop modules denote a pool being relatively safe.
  * If a large percentage of the backstop module is queued for withdrawal, the pool is probably in an unstable state, and the user should avoid it for the time being.
* Ensure the assets supported as collateral in the pool are safe collateral assets.
  * Generally, low volatility, high liquidity assets make good collateral.
* Ensure the pool's risk parameters are set appropriately.
  * The pool should have reasonable collateral and liability factors for supported assets.
* Ensure the pool's oracle contract is trustworthy.
  * Oracles are crucial for the safety of pools — users should always be sure the oracle for their lending pool is reliable.

Once a lending pool is selected, borrowers deposit collateral in it and can borrow assets from it.

Borrowers can repay borrowed assets from the protocol at any time.

### Why Would Users Borrow From Blend?

In exchange for borrowing from Blend lending pools, borrowers receive BLND issuance from the protocol (if their pool is in the reward zone and is allocating emissions to borrowers of the asset the user is borrowing). In addition, users retain full control of their collateral when borrowing from Blend. The protocol is decentralized, trust-free, and non-custodial. Only the protocol smart contracts have control over user funds, and users can withdraw their funds at any time.

### What Assets can be Borrowed?

Any Stellar-based asset enabled for borrowing by the lending pool the user is using can be borrowed with Blend. If an asset isn't supported, a new pool can be created to support it as long as the pool can attract sufficient backstop module deposits to reach the [minimum deposit threshold](../../blend-whitepaper.md#backstop-threshold).

### How do Borrowers Repay Borrowed Assets?

Borrowers can repay borrowed assets by using Blend's smart contracts. In doing so, they will also repay all interest the borrower has accrued (interest is automatically accrued to the user's liability balance).

### How Much Can Users Borrow?

Each user has a borrow limit for pools they are using based on the amount and quality of their collateral. If they exceed this borrow limit, their account can be liquidated. Users can calculate their borrow limit using the following equation:

$$BorrowLimit = \sum{CollateralFactor*CollateralValue}$$

Users should note that when borrowing, their borrow limit is increased by the liability asset's value divided by its liability factor, so:

$$EffectiveLiabilityValue = \frac{LiabilityValue}{LiabilityFactor}$$

### How Do Borrowers Pay Interest?

Interest automatically accrues to a borrower's liability balance over time. When they repay their debt, they will also be paying any interest they owe.

### How Is Interest Calculated?

Interest rates vary based on pre-set interest rate parameters but are generally based on the utilization of an asset in a pool (how much of the supplied amount is borrowed). In addition, interest can move higher or lower if an asset is consistently over or underutilized due to the interest rate model's reactivity.

To learn more about how Blend calculates interest rates, see the [interest rate section](../../blend-whitepaper.md#interest-rates) of the whitepaper.

### Do Borrowers Earn BLND Emissions?

Borrowers earn BLND emissions if their pool is in the reward zone, and the pool creator allocated emissions to borrowers of the asset the user is borrowing. They may claim these emissions at any time.
