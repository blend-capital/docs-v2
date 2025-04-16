# Adding Assets

Pool Creators are responsible for adding assets to their pool. The assets they add will depend on the intended use case for their pool.\
\
When they add assets, they must also set the asset's [risk](risk-parameters.md), [interest rate](interest-rates.md), and [emission](emissions.md) parameters.

Pool Admins can add and remove assets from the pool and modify their parameters. This is important to note as pool creators should opt for an Owned Pool over a Standard Pool if they require flexibility with assets in the future.

Assets can be anything that follows the standard token interface. This includes native stellar tokens, bridged assets, or even custom tokens such as credit tokens.

A pool cannot have more than 30 assets.
