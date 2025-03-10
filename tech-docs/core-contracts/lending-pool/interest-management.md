# Interest Management

The Pool contract is used to distribute the backstop's share of pool interest to the pool's backstop depositor

## Creating Interest Auctions

Interest is distributed to backstop depositors through a backstop interest auction. These are similar to Liquidation Auctions except they sell interest instead of collateral and buy backstop LP tokens instead of debt repayment. The backstop interest auction is created by calling the `create_interest_auction()` function and specifying which assets interest should be distributed for. All interest for these assets will be put up for auction and 140% of their value in backstop tokens will be requested in return.

Similar to liquidation auctions - the percent of posted interest transferred to the liquidator increases by .05% every block for the first 200 blocks - and the percent of posted backstop tokens transferred to the pool decreases by .05% every block for the second 200 blocks.

## Filling Interest Auctions

Backstop interest auctions are filled by calling the `submit()` function with a `fill_interest_auction` `Request`.

When an interest auction is filled, the accrued interest is transferred to the liquidator - and backstop tokens used to fill the auction are transferred to the pool and donated to the backstop. This increases the value of the pool's backstop deposits.
