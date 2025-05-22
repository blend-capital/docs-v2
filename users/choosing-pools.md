# Choosing Pools

Users should be careful when choosing a pool to use — below are some things for the users to pay attention to:

* Check if the pool supports the asset they want to lend, borrow, or collateralize.
  * Assets supported for lending and borrowing will have a [liability factor](lending-borrowing/borrowing.md#how-much-can-users-borrow) above 0 and show an interest rate above 0.
  * Assets supported as collateral will have a [collateral factor](lending-borrowing/borrowing.md#how-much-can-users-borrow) above 0.
* Ensure the pool has a well-capitalized backstop module.
  * The backstop module is a fund of assets insuring the pool against bad debt. Well-capitalized backstop modules denote a pool being relatively safe, as backstop module depositors will not want to insure unsafe pools.&#x20;
  * If a large percentage of the backstop module is queued for withdrawal, the pool is probably in an unstable state — and the user should avoid it for the time being.
* Ensure the assets supported as collateral in the pool are safe collateral assets.
  * Generally, low volatility and high liquidity assets make good collateral.
* Ensure the pool's [risk parameters](../pool-creators/adding-assets/risk-parameters.md) are set appropriately.
  * The pool should have reasonable collateral and liability factors for supported assets.
* Ensure the pool's oracle contract is reliable.
  * Lending pools rely on oracles to fetch asset prices — users should always be sure the oracle for their lending pool is reliable, or their assets may be lost.
* Ensure the pool admin is trustworthy
  * The pool admin has the ability to change the majority of these settings, so trusting them is a requirement for using the pool.
  * Some pools use disabled public keys as the admin. If this is the case, the pool is immutable and no configuration options can be changed.
