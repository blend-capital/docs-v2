# Liquidations

In the case that a user's account becomes delinquent because they exceed their [borrow limit,](borrowing.md) they can be liquidated. This means their positions will be transferred to another Blend user who will then either close them or re-collateralize them.

### How do Blend Liquidations Work?

When a Blend user becomes delinquent, any user may initiate a liquidation auction on their account — the liquidator will choose a percent of the user's collateral positions to be auctioned off. They cannot auction off more liabilities than are necessary to bring the account back to a healthy state. Based on the percentage of liabilities being auctioned off, the protocol will choose a percentage of the user's collateral to also be auctioned off. The value of the collateral auctioned will slightly exceed the value of the liabilities being auctioned. \
\
As soon as the auction is initiated, it begins. At this point, anyone can fill the auction. The percent of the offered collateral the auction filler receives depends on how long the auction has gone on (the percent grows over time). Once 100% of the offered collateral is available, the percent of the liability that the filler must repay starts decreasing from 100% until it hits 0%. This way, we can be sure that the auction will be filled as soon as a market participant deems it profitable.&#x20;

When a liquidator fills the auction, the filled liability and collateral positions are transferred to their account. Then, the liquidator must ensure their account is healthy by either posting enough collateral to maintain the newly acquired liabilities or by repaying a portion of the liabilities.&#x20;

Since liquidators have autonomy over when to fill the auction, they will typically fill it at a point where the value of the collateral they receive slightly exceeds the value of the liabilities they repay.

For a full explanation of the liquidation process, see the [liquidation section](../../blend-whitepaper.md#liquidations) of the whitepaper.
