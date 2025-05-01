# Tutorial: Setting Up a Pool

Here we'll walk through a step-by-step guide to deploying and setting up a new pool. The deployment should be carried out using the blend\_utils repo which has scripts for doing so.

NOTE: this guide may be out of date - we recommend cross-referencing with the up to date guide here: [https://github.com/blend-capital/blend-utils?tab=readme-ov-file#pool-deployment](https://github.com/blend-capital/blend-utils?tab=readme-ov-file#pool-deployment)

## Step 1: Decide on an oracle

The oracle is backbone of a pool, and cannot be changed once an oracle is set.

Please review [Selecting an Oracle](selecting-an-oracle.md).

## Step 2: Deploy a new Pool Contract

We first deploy a new pool contract by calling the `deploy()` function on the Pool Factory Contract. The function takes the following parameters:

| Parameter            | Type                                                                                      | Description                                                                                                                                                          |
| -------------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| admin                | <pre class="language-rust"><code class="lang-rust">Address
</code></pre>                  | Address of the pool admin. More Information : [pool-management.md](../tech-docs/core-contracts/lending-pool/pool-management.md "mention")                            |
| name                 | <pre class="language-rust"><code class="lang-rust">String
</code></pre>                   | The Pool name. Used by UI's to render a name for the pool.                                                                                                           |
| salt                 | <pre class="language-rust"><code class="lang-rust">BytesN&#x3C;32>
</code></pre>          | Random bytes used to generate the pool contract address                                                                                                              |
| oracle               | <pre class="language-rust"><code class="lang-rust"><strong>Address
</strong></code></pre> | Address of the oracle contract used by the pool. More Information: [selecting-an-oracle.md](selecting-an-oracle.md "mention")                                        |
| backstop\_take\_rate | <pre class="language-rust"><code class="lang-rust"><strong>u32
</strong></code></pre>     | <p>Pool backstop take rate. Scaled to 7 decimals.<br>More Information:<br><a data-mention href="setting-backstop-take-rate.md">setting-backstop-take-rate.md</a></p> |
| max\_positions       | <pre class="language-rust"><code class="lang-rust"><strong>u32
</strong></code></pre>     | Pool max positions. No decimals. More Information: [setting-max-positions.md](setting-max-positions.md "mention")                                                    |
| min\_collateral      | <pre class="language-rust"><code class="lang-rust"><strong>i128
</strong></code></pre>    | The minimum collateral amount required to open a borrow position. Should be set higher than the gas cost required to liquidate the position (e.g. $1). Scaled to the oracle's decimals.             |

The deployment function will return the new Pool Contract's address.

{% hint style="info" %}
The Pool Factory contract address can be found here: [mainnet-deployments.md](../mainnet-deployments.md "mention")
{% endhint %}

{% hint style="warning" %}
Many of the pool setup functions can only be called by the pool admin. If the pool creator intends for the admin to be a DAO or multisig it may be preferable for them to continue using their account to finish setting up the pool, then transfer pool ownership to the final admin using the Pool Contract's `set_admin()` function.
{% endhint %}

## Step 3: Add Pool Reserves

After the pool is deployed we add assets (reserves) to it by calling the Pool Contract's `queue_set_reserve()` function. The function takes the following parameters:

<table><thead><tr><th>Parameter</th><th width="185">Type</th><th>Description</th></tr></thead><tbody><tr><td>asset</td><td><pre class="language-rust"><code class="lang-rust">Address
</code></pre></td><td>Token Contract address of the asset being added to the pool</td></tr><tr><td>metadata</td><td><pre class="language-rust"><code class="lang-rust">ReserveConfig
</code></pre></td><td>The reserve configuration for the new asset.</td></tr></tbody></table>

The ReserveConfig struct is constructed as follows:

```rust
pub struct ReserveConfig {
    pub index: u32,      // the index of the reserve in the list
    pub decimals: u32,   // the decimals used in both the bToken and underlying contract
    pub c_factor: u32,   // the collateral factor for the reserve scaled expressed in 7 decimals
    pub l_factor: u32,   // the liability factor for the reserve scaled expressed in 7 decimals
    pub util: u32,       // the target utilization rate scaled expressed in 7 decimals
    pub max_util: u32,   // the maximum allowed utilization rate scaled expressed in 7 decimals
    pub r_base: u32, // the R0 value (base rate) in the interest rate formula scaled expressed in 7 decimals
    pub r_one: u32,  // the R1 value in the interest rate formula scaled expressed in 7 decimals
    pub r_two: u32,  // the R2 value in the interest rate formula scaled expressed in 7 decimals
    pub r_three: u32, // the R3 value in the interest rate formula scaled expressed in 7 decimals
    pub reactivity: u32, // the reactivity constant for the reserve scaled expressed in 7 decimals
    pub collateral_cap: i128, // the total amount of underlying tokens that can be used as collateral
    pub enabled: bool,        // the enabled flag of the reserve
}
```

* The reserve `index` will be assigned by the `set_reserve()` function, the parameter does not matter when setting up the asset.
* The reserve `decimals` must match the number of decimals the new asset has, as defined by the asset's Token Contract. Stellar Classic assets have 7 decimals.
* More information on reserve `c_factor`, `l_factor`, and `max_util`, can be found here: [risk-parameters.md](adding-assets/risk-parameters.md "mention")
* More information on reserve `util`, `r_base`, `r_one`, `r_two`, `r_three`, and `reactivity` can be found here: [interest-rates.md](adding-assets/interest-rates.md "mention")

After the reserve is queued we finalize the addition by calling `set_reserve()` function. The function takes the following parameters:

<table><thead><tr><th>Parameter</th><th width="281">Type</th><th>Description</th></tr></thead><tbody><tr><td>asset</td><td><pre class="language-rust"><code class="lang-rust">Address
</code></pre></td><td>Token Contract address of the asset being added to the pool</td></tr></tbody></table>

{% hint style="warning" %}
Pool creator's should be careful not to update the pool status before adding all initial reserves. If they do there will be a manditory 7 day delay between calling `queue_set_reserve() and set_reserve()`
{% endhint %}

## Step 4: Set up Pool Emissions

After reserves are added, we set up pool emissions by calling the `set_emissions_config()` function. The function takes the following parameters:

<table><thead><tr><th width="228">Parameter</th><th width="185">Type</th><th>Description</th></tr></thead><tbody><tr><td>res_emission_metadata</td><td><pre class="language-rust"><code class="lang-rust"><strong>Vec&#x3C;ReserveEmissionMetadata>
</strong></code></pre></td><td>A vector of ReserveEmissionMetadata structs. These structs govern how pool emissions are distributed between pool assets.</td></tr></tbody></table>

The ReserveEmissionMetadata struct is constructed as follows:

```rust
pub struct ReserveEmissionMetadata {
    pub res_index: u32, // index of the reserve as defined by the ReserveConfig
    pub res_type: u32,  // 0 for liabilities, 1 for supply
    pub share: u64, // percent of the total pool emissions this reserve should receive. Scaled to 7 decimals
}
```

* `res_index` is the index of the reserve defined by the ReserveConfig struct. It was returned by the `set_reserve()` function. Reserve index's are set based on the order the reserves were added to the pool (the first reserve will have index 0, the next index 1, etc.).
* `res_type` designates whether lenders or borrowers of the reserve will receive emissions. If liabilities are designated borrowers receive emissions, if supply is designated lenders receive emissions. It is possible for both to receive emissions, the vec input into the `set_emissions_config()` function just must include a `ReserveEmissionMetadata` struct for both reserve liabilities and supply.
* `share` is the percent of total pool emissions the reserve (and reserve type) should receive. The total of all input `ReserveEmissionMetadata` share's must equal `10000000`.

## Step 5 (optional): Set up Pool Backstop

If the pool creator wishes to, they can fund the pool's backstop at this point so that the pool meets the minimum backstop threshold allowing it to be activated and potentially added to the backstop reward zone.

**It is highly recommended to test your pool before depositing into the backstop**.

For example, is there is a reserve that cannot have a priced fetched on the oracle, you will not be able to use the pool.

An easy way to preview pool configuration is to navigate to your pool via the Blend UI. Searching `/dashboard?poolId=C...` will attempt to load the pool via the UI.

#### How many Backstop Tokens do I need?

To reach the minimum backstop threshold a blend pool's backstop must have enough backstop LP tokens for their associated reserves to reach a product constant value of 200,000. This can be calculated with the following formula

$$
k=x^{0.8}*y^{0.2}
$$

Where:

* k = Product Constant
* x = Blend Amount
* y = USDC amount

At current rates each LP token's associated reserves have a product constant of around 2. So 50,000 LP tokens are sufficient to reach the backstop threshold.

### Acquiring Backstop Tokens

To fund the backstop module we first need to acquire backstop tokens (80:20 BLND:USDC comet LP tokens). These can be acquired in 3 different ways depending on the assets we have on hand.

Individuals based outside of restricted jurisdictions can acquire backstop tokens via any of these options using the Blend UI [https://mainnet.blend.capital/](https://mainnet.blend.capital/)

#### Option 1: Acquiring Backstop tokens using only USDC

To acquire backstop tokens with only USDC we must execute a single sided deposit on the Comet Liquidity Pool by calling the `dep_lp_tokn_amt_out_get_tokn_in` function. The function has the following parameters:

| Parameter         | Type                                                                                      | Description                                                                |
| ----------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| token\_in         | <pre class="language-rust"><code class="lang-rust">Address
</code></pre>                  | Address of the token being deposited (in this case USDC)                   |
| pool\_amount\_out | <pre class="language-rust"><code class="lang-rust">i128
</code></pre>                     | The number of pool tokens to mint                                          |
| max\_amount\_in   | <pre class="language-rust"><code class="lang-rust">i128
</code></pre>                     | The maximum amount of tokens (in this case USDC) you're willing to deposit |
| user              | <pre class="language-rust"><code class="lang-rust"><strong>Address
</strong></code></pre> | Address of the user depositing                                             |

#### Option 2: Acquiring Backstop Tokens using Both BLND and USDC

We use the Comet Liquidity Pool's `join_pool` function to mint backstop tokens using both BLND and USDC. It has the following parameters:

| Parameter         | Type                                                                                             | Description                                                                                                     |
| ----------------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| pool\_amount\_out | <pre class="language-rust"><code class="lang-rust">i128
</code></pre>                            | The number of pool tokens to mint                                                                               |
| max\_amounts\_in  | <pre class="language-rust"><code class="lang-rust"><strong>Vec&#x3C;i128>
</strong></code></pre> | The maximum amount of tokens you're willing to deposit. BLND amount is first in the Vec, USDC amount is second. |
| user              | <pre class="language-rust"><code class="lang-rust"><strong>Address
</strong></code></pre>        | Address of the user depositing                                                                                  |

#### Option 3: Acquiring Backstop Tokens using BLND

To acquire backstop tokens with only BLND we execute a single sided deposit on the Comet Liquidity Pool by calling the `dep_lp_tokn_amt_out_get_tokn_in` function. This is much the same as option 1, except the deposit token is BLND and the max amount in is the maximum BLND you're willing to deposit.

### Depositing Backstop Tokens

We can deposit backstop tokens into the backstop by calling `deposit()` on the Backstop contract. It has the following parameters:

| Parameter     | Type                                                                                      | Description                                                                                                |
| ------------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| from          | <pre class="language-rust"><code class="lang-rust">Address
</code></pre>                  | The address of the user depositing the tokens                                                              |
| pool\_address | <pre class="language-rust"><code class="lang-rust"><strong>Address
</strong></code></pre> | The address of the pool the backstop deposit is being made to. In this case it's the address of your pool. |
| amount        | <pre class="language-rust"><code class="lang-rust"><strong>i128
</strong></code></pre>    | The number of backstop tokens you wish to deposit.                                                         |

### Other Options to Fund the Backstop

If the pool creator does not wish to fund the entire backstop themselves they have 2 options.

#### Backstop Bootstrapping

A pool creator with a large amount of BLND or USDC that does not wish to create significant market impact by setting up their backstop can choose to use the Backstop Bootstrapper contract to facilitate a community backstop deposit.

More details can be found here:

[backstop-bootstrapping.md](backstop-bootstrapping.md "mention")

#### Community Funding

If the creator does not want to put much (or any) capital into the pool's backstop they can still deploy the pool without depositing into the backstop and just publicize the deployment to the blend community, inviting them to deposit.

{% hint style="info" %}
The contract addresses for the Comet Pool contract, the Backstop contract, and the Backstop Bootstrapper contract can be found here: [mainnet-deployments.md](../mainnet-deployments.md "mention")
{% endhint %}

## Step 5: Activating the Pool

Now that all pool parameters are set we need to turn the pool on. We can do so using the Pool contract's `set_status()` function. It has the following parameters:

<table><thead><tr><th>Parameter</th><th width="281">Type</th><th>Description</th></tr></thead><tbody><tr><td>pool_status</td><td><pre class="language-rust"><code class="lang-rust">u32
</code></pre></td><td><p>Pool Status:</p><ul><li>0 = admin active - requires that the backstop threshold is met. Allows both deposits and borrowing</li><li>2 = admin on-ice - allows deposits but not borrowing</li><li>4 = admin frozen - does not allow deposits or borrowing</li></ul></td></tr></tbody></table>

Here we recommend that you set the pool to `on_ice` then, if the backstop has been funded, call the Pool contract's `update_status()` function (no parameters) which will set the status to active. Admin active is a elevated status which makes it harder for the backstop to shut the pool down.

## Step 6 (optional): Add Pool to Reward Zone

At this point if the pool's backstop was funded in Step 4 so that it has met the backstop threshold we can add the pool to the Backstop Contract's reward zone so it begins receiving emissions.

We do so by calling the `add_reward()` function on the Backstop contract. It has the following parameters:

| Parameter  | Type                                                                                      | Description                                                                                                                                                                                                                                                                                                             |
| ---------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| to\_add    | <pre class="language-rust"><code class="lang-rust">Address
</code></pre>                  | The address of the pool being added to the reward zone. In this case your pool's contract address.                                                                                                                                                                                                                      |
| to\_remove | <pre class="language-rust"><code class="lang-rust"><strong>Address
</strong></code></pre> | The address of the pool being removed from the reward zone. This parameter only matters if the reward zone is full (it currently isn't), in which case your pool must have a larger backstop than one currently in the reward zone. In that case this parameter will be the address of the pool your pool is replacing. |

And that's it! Your done! If you've completed all 6 steps your pool will now show up in the markets page at [https://mainnet.blend.capital/](https://mainnet.blend.capital/)
