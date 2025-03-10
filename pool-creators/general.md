# General

Anyone can deploy an isolated lending pool using Blend. All they need to do is:

* [Select assets and their parameters](adding-assets/)
* [Decide on a pool management strategy](pool-management.md)
* [Decide on an oracle](selecting-an-oracle.md)
* [Select a backstop take rate](setting-backstop-take-rate.md)

### Who would create a lending pool?

#### For network platforms, like wallets and DEX interfaces

* Platform-specific lending pools allow platforms to offer their users yield products, leveraged trading, borrowing, and more in a non-custodial fashion while maintaining a high level of control over the specific lending market available to their users. They can create “owned” lending markets that they have the power to update post-deployment.

#### For anchors, Blend offers

* The ability to create lending markets that feature the anchor’s assets, improving asset usefulness, liquidity, and adoption. New markets and assets do not require the approval of a DAO (Blend has no DAO); instead, they must reach a [certain level of backstop module deposits](../blend-whitepaper.md#backstop-threshold) before being enabled. This allows Anchors to quickly create new markets when they introduce new assets.
  * An additional benefit here is anchors can support liquidity pool shares featuring their assets as collateral. This allows stablecoins to build large amounts of liquidity when users loop liquidity pool shares (borrow against them, turn the borrowed deposits into more liquidity pool shares, and redeposit to repeat).

#### For DeFi protocols, Blend offers

* The ability to create CDP protocols like MakerDAO. A lending pool is just a CDP vault with multiple assets; it would be simple for a CDP protocol to create a series of DAO-managed pools where users can borrow a debt-based stablecoin like DAI. This could be especially useful on the Stellar network to give users access to currencies that still lack established anchors.
* The ability to create DAO-managed mutable lending pools like Compound or Aave. By setting a Governance smart contract as a pool owner, someone could easily recreate a Compound or Aave like product on Stellar.
* RWA lending protocols can create custom markets that provide whitelisted companies with lines of credit tokens. This would allow approved entities to borrow from the pool without having to post collateral, enabling them to fund loans with on-chain assets. We expect this use case to be particularly useful given Stellar’s recent MoneyGram partnership and the difficulty of procuring financing in many developing economies.
