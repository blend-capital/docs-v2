# Risk Parameters

Pool creators use the following parameters to control asset risk. These parameters must be set for each asset in the pool.

### Collateral Factor

An asset's collateral factor modifies the asset value when used as collateral. Asset collateral factors must be set to less than or equal to 1.

When used as collateral, an asset's value is calculated using the following equation.

$$Effective CollateralValue= CollateralFactor * CollateralValue$$

Generally, an asset's collateral factor should be set lower the riskier an asset is. If an asset shouldn't be collateralized at all, the collateral factor should be set to 0.

### Liability Factor

An asset's liability factor modifies the asset value when borrowed. Liability factors must be set to less than or equal to 1.

An asset's value, when borrowed, is calculated using the following equation.

$$EffectiveLiabilityValue=LiabilityValue*LiabilityFactor$$

Generally, an asset's liability factor should be set lower the riskier an asset is. If an asset isn't intended to be borrowed, its liability factor should be set to 0.

### Utilization Cap

Pool creators can set asset utilization caps to prevent more than a certain percentage of an asset from being borrowed. This parameter is useful for protecting lenders in the case of an oracle failure. For example, by setting the utilization cap of assets primarily used as collateral to 25%, no more than 25% of deposits can be stolen during an oracle attack.

### Supply Cap

Pool creators can set a collateral cap for assets. This limits the total number of tokens that can be supplied to the pool. Pool creators should use this parameter to limit the pools exposure to long tail and volatile assets they want to use as collateral. It's also VERY important that they use it to protect the pool from issuer risks. Centralized assets (real world assets and stablecoins) can be infinitely minted by their issuers if the issuer becomes malicious or compromised. Setting collateral caps on these assets limits the amount of damage that can be done if such a scenario occurs.&#x20;

