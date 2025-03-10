# Setting Max Positions

Pool creators must set the maximum number of positions user's in their pool can have. This just refers to the maximum number of positions a user can have at any given time. This is a pool-level setting, and can be changed by the pool admin at any time.

Pool's with lower numbers of max positions are more stable as user's positions are easier and less disruptive to liquidate. However, they're less attractive to user's that want more flexibility in their position complexity.

Pool creators must be very careful to not set this parameter too high as it can lead to positions being unliquidatable due to Sorban resource limits as it's cheaper to open new positions than liquidate all positions. Typically safe limits are:

- 4 for Stellar classic assets TODO: confirm
- 2 for Soroban native assets (this can vary with complex custom assets contracts)

These limits will change as Soroban resource limits increase - see: https://soroban.stellar.org/docs/reference/resource-limits-fees
