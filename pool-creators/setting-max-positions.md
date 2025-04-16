# Setting Max Positions

Pool creators must set the maximum number of positions user's in their pool can have. This just refers to the maximum number of positions a user can have at any given time. This is a pool-level setting, and can be changed by the pool admin at any time.

Pool's with lower numbers of max positions are more stable as user's positions are easier and less disruptive to liquidate. However, they're less attractive to user's that want more flexibility in their position complexity.

Pool creators must be very careful to not set this parameter too high as it can lead to positions being unliquidatable due to Sorban resource limits as it's cheaper to open new positions than liquidate all positions. This is due to resource constrains around the oracle the pool uses. If you use the oracle aggregator linked in his documentation and the Reflector Oracle safe limits are:

* 6 for Stellar classic assets&#x20;
  * This depends heavily on oracle and asset configurations. 6 is the reccomendation with the standard oracle aggregator linked in [https://app.gitbook.com/o/-Me0aIJ5ubY2Yyeo6UbM/s/Lg1UeA72WAt02V2TIIga/\~/changes/7/pool-creators/selecting-an-oracle](selecting-an-oracle.md)
* Assets deployed on Soroban have larger resource requirements than Stellar Classic assets so any pool creator utilizing these assets should test the limits themselves.&#x20;

These limits will change as Soroban resource limits increase - see: https://soroban.stellar.org/docs/reference/resource-limits-fees
