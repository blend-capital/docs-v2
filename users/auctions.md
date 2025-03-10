# Auctions

### What are auctions in Blend?

The Blend protocol utilizes auctions to sell assets on behalf of the protocol to a bidder. Each auction contains a lot (what the bidder receives) and a bid (what the bidder pays). The auctions are Dutch auctions, or descending price auctions, meaning the price for the bidder starts out extremely high and descends over time. Anyone can fill auctions conducted by the Blend protocol, provided you have the ability to hold the assets in the lot. There are three different types of auctions conducted by the protocol: interest, liquidations, and bad debt.

Auctions can be filled by invoking the `submit` function on a Blend pool, with the following Request included:

Request

* amount `i128`
  * The percentage of the auction to fill as an integer, from 1-100.
* request\_type `u32`
  * The type of auction to fill (6 = liquidation, 7 = bad debt, 8 = interest).
* address `Address`
  * The owner of the auction. This is the backstop address for bad debt and interest auctions, and the user being liquidated for liquidation auctions.

### How are auctions priced?

The auctions are priced based on how many blocks have passed since the auction began, over the course of 400 blocks. The initial price of auctions are set differently based on each auction, but on creation each auction will be set with base values for each asset in the lot and bid. This base value is then scaled for each asset based on the amount of blocks that have passed:

$$
Lot(N) = \begin{cases} Lot_{base}*\frac{1}{200}N & \text{if } N\lt 200\\ Lot_{base} & \text{if } N\geq 200\\ \end{cases}
$$

$$
Bid(N) = \begin{cases} Bid_{base} & \text{if } N\leq 200\\ Bid_{base}*(1-\frac{1}{200}(N-200)) & \text{if } N\gt 200\\ \end{cases}
$$

At block 0, the bidder pays the full bid but receives nothing. Between block 0 and 200, the lot increases by 0.5% of the base. At block 200, the bid and lot are exactly the base value of the auction. Between block 200 and 400, the bid decreases by 0.5% of the base. At block 400, the bidder pays nothing but receives the full lot.

### Interest Auctions

Interest auctions facilitate the sale of the backstop's earned interest for backstopping a pool in exchange for backstop tokens. These backstop tokens are then donated to the backstop such that they are shared among all backstop depositors.

#### Bid

The bid contains only the [backstop token](backstopping.md#what-is-the-backstop-token). On a successful fill, the backstop token is transferred from the bidder to the pool.

#### Lot

The lot contains all tokens that have generated interest due to the backstop for a pool. This generally is all borrowable assets in a pool. On a successful fill, the pool transfers the lot to the bidder.

### Liquidation Auctions

Liquidation auctions facilitate the sale of a liquidated users collateral in exchange for taking on their liabilities. Filling these auctions generally results in the bidder taking on more liabilities than collateral, so it is recommended that bidders have some collateral already present in the pool, or they also include repayment requests in the same transaction. If the bidder does not end up with a healthy position after the transaction, the fill will revert.

#### Bid

The bid contains a set of [dTokens](../tech-docs/core-contracts/lending-pool/protocol-tokens.md#dtokens) currently held by the liquidated user. On a successful fill, the dTokens in the bid are moved from the liquidated user to the bidder. The bidder now has a liability against the pool that they can decide when and how to repay.

#### Lot

The lot contains a set of [bTokens](../tech-docs/core-contracts/lending-pool/protocol-tokens.md#btokens) currently held by the liquidated user. On a successful bid, the bTokens in the lot are moved from the liquidated user to the bidder. The bidder now has additional collateral against the pool that they can decide when and how to withdraw.

### Bad Debt Auctions

Bad debt can be generated as a result of liquidations. If a liquidated user does not have any collateral left but still maintains some liabilities, these liabilities are considered "bad debt" and moved to the backstop. Bad debt auctions facilitate the sale of backstop tokens in exchange for taking on the bad debt.

#### Bid

The bid contains a set of [dTokens](../tech-docs/core-contracts/lending-pool/protocol-tokens.md#dtokens) currently held by the backstop. On a successful fill, the dTokens in the bid are moved from the backstop to the bidder. The bidder now has a liability against the pool that they can decide when and how to repay.

#### Lot

The lot contains only the [backstop token](backstopping.md#what-is-the-backstop-token). On a successful fill, the backstop token is transferred from the backstop to the bidder.
