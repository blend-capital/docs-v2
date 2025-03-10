# Deploying a Pool

## Deploying A New Lending Pool

This guide will walk you step-by-step through deploying a new lending pool using the scripts in the blend-utils repository(https://github.com/blend-capital/blend-utils).

## Prerequisites

The following decisions and actions must be made before deploying a pool.

### Funding the Pool's Backstop

To activate a pool, and to receive BLND emissions, the pool's backstop module must be funded with a minimum amount of BLND:USDC 80:20 liquidity pool shares.

In order to activate a pool the pool's backstop must meet the minimum backstop threshold (https://docs.blend.capital/whitepaper/blend-whitepaper#backstop-threshold).

{% hint style="info" %}
**Example:**\
With the following pool state:

- 10,000,000 BLND:USDC 80:20 liquidity pool shares
- 20,000,000 BLND liquidity pool balance
- 500,000 USDC liquidity pool balance
- fee of 0.3% The required liquidity pool shares to activate the pool would be 104,664.91 BLND:USDC 80:20 liquidity pool shares.\\

      $$
      Ktotal = 20m^{0.8} * 500k^{0.2} = 9,563,525
      $$

      $$
      kPerShare = \frac{Ktotal}{10m} = 0.96
      $$

      $$
      sharesRequired = \frac{100,000}{kPerShare} = 104,664.91
      $$

      **Balanced Deposit Requirement:**\\

      $$
      requiredBLND = \frac{sharesRequired}{20m*Ktotal} = 218,672.42
      $$

      $$
      requiredUSDC = \frac{sharesRequired}{500k*Ktotal} = 5,466.81
      $$

      **USDC Deposit Requirement:**\
      we increase required shares by the fee amount to account for the swap fee and slippage realized in a single sided deposit.\\

      $$
      requiredUSDC = -\frac{\frac{10m-sharesRequired*1.003}{10m}^(1/0.2)*500k-500k}{1-(0.003*(1-0.2))} = 25,202.79
      $$

      **BLND Deposit Requirement:**\
      we increase required shares by the fee amount to account for the swap fee and slippage realized in a single sided deposit.\\

      $$
      requiredBLND = -\frac{\frac{10m-sharesRequired*1.003}{10m}^(1/0.8)*20m-20m}{1-(0.003*(1-0.8))} = 261,660.02
      $$

  {% endhint %}

To receive BLND emissions a pool must have sufficient BLND to be added to the backstop reward zone (https://docs.blend.capital/whitepaper/blend-whitepaper#reward-zone).

If the reward zone is empty there is no requirement beyond meeting the backstop to be added to the reward zone. If the reward zone is full, the pool can replace another pool in the reward zone as long as it has more backstop deposits than the pool it is replacing.

If the pool creator does not want to fund the backstop themselves, they can still deploy and rely on others to fund the backstop before the pool can be activated or receive rewards.

Pool creators using the blend-utils repository to deploy a pool can designate whether and how to fund their backstop by setting the following consts in the `deploy_pool.js` script:

- `deposit_asset` - The asset that will be deposited into the BLND:USDC pool to fund the deployed pool's backstop. `0` denotes BLND, `1` denotes USDC, and `2` denotes a balanced deposit of both BLND and USDC.
- `blnd_max` - The maximum amount of BLND that can be deposited into the liquidity pool. This parameter is not used if the `deposit_asset` isn't `0` or `2`.
- `usdc_max` - The maximum amount of USDC that can be deposited into the liquidity pool. This parameter is not used if the `deposit_asset` isn't `1` or `2`.
- `mint_amount` - The number of liquidity pool tokens to mint for the user. The minted tokens will be deposited into the deployed pool's backstop.

They may also specify the starting status for their pool by setting the `status` const in the `deploy_pool.js` script. The pool's status can be set to `0` for `Active` (as long as the minimum backstop threshold was met), `2` for `Admin-On-Ice`, `3` for `On-Ice`, and `4` for `Admin-Frozen`. For more information on pool status see: https://docs.blend.capital/tech-docs/core-contracts/lending-pool/pool-management

Finally, they can designate whether or not to add the pool to the reward zone with the `add_to_reward_zone` const in the `deploy_pool.js` script. And the pool to replace (if necessary) with the `pool_to_remove` const.

### Managed vs Unmanaged Pools

Pools can be managed or unmanaged. Managed pools have a designated admin that has the authority to change pool status or update asset risk/interest rate parameters. Alternatively, the pool's admin address can be set to a dead address, which makes the pool immutable, although its status can still be changed by the backstop module.

Pool creators using the blend-utils repository to deploy a pool can check the readme to see how to deploy a managed or unmanaged pool.

### Deciding on Pool Parameters

Before deploying a pool, you must decide on the following pool parameters:

- pool-name: The name of the pool.
- (backstop-take-rate)\[https://docs.blend.capital/pool-creators/setting-backstop-take-rate]
- (max-positions)\[https://docs.blend.capital/pool-creators/setting-max-positions]

Pool creators using the blend-utils repository to deploy a pool can check the readme to see how to set these parameters for pools deployed with the `deploy-pool.js` script.

### Deciding on Asset Parameters

After choosing the assets to include in the pool, you must decide on risk, interest, and emission parameters for each asset. For more information on asset parameters see: https://docs.blend.capital/pool-creators/adding-assets

Pool creators using the blend-utils repository to deploy a pool can check the readme to see how to set these parameters for assets deployed with the `deploy-pool.js` script.

## Deploying a Pool with blend-utils

The blend-utils repository contains a script to make pool deployment easy and straightforward. Instructions on how to use the script can be found in the blend-utils repository readme.

### Example Pool Deployment

Here we will show an example of how to deploy a new pool using the blend-utils repository. TODO: update once deploy-pool.js is updated.

#### Step 1: Set up the blend-utils repository

First, clone the blend-utils repository

```bash
git clone https://github.com/blend-capital/blend-utils
```

and navigate to the blend-utils directory.

```bash
cd blend-utils
```

Next, install the dependencies

```bash
npm i
```

#### Step 2: Set up your .env file

```
# The URL of the RPC server
RPC_URL=https://soroban-testnet.stellar.org

# The URL of the friendbot server (only used for non-mainnet networks)
FRIENDBOT_URL=https://friendbot-testnet.stellar.org

# The passphrase of the network to connect to
NETWORK_PASSPHRASE=Test SDF Network ; September 2015

# The strkey encoded secret key to use for the deployment / admin account
ADMIN=S...

```

#### Step 3: Ensure the testnet.contracts.json file is up to date

```json
{
  "ids": {
    "oracle": "CA2NWEPNC6BD5KELGJDVWWTXUE7ASDKTNQNL6DN3TGBVWFEWSVVGMUAF",
    "XLM": "CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC",
    "USDC": "CAQCFVLOBK5GIULPNZRGATJJMIZL5BSP7X5YJVMGCPTUEPFM4AVSRCJU",
    "BLND": "CB22KRA3YZVCNCQI64JQ5WE7UY2VAV7WFLK6A2JN3HEX56T2EDAFO7QF",
    "comet": "CBESO2HJRRXRNEDNZ6PAF5FXCLQNUSJK6YRWWY2CXCIANIHTMQUTHSOM",
    "backstop": "CAYRY4MZ42MAT3VLTXCILUG7RUAPELZDCDSI2BWBYUJWIDDWW3HQV5LU",
    "emitter": "CAKCA6QKNT7BD4PZ3DMNFSUKD5CVD33GMZW53UF5GEBZNHEGBKBLVJY5",
    "poolFactory": "CB5UDFTJ6VFOK63ZHQASNODV4PP2HVGPYRF754LRGO7YRG5SFCAZWTDD"
  },
  "hashes": {
    "emitter": "d1f383171e86325d8c5efa12b219c3afda90d4469541b10ff93b666351ad5b42",
    "poolFactory": "f3abb9e2c5106a75ac1a45c177c21fa3820f77aef7229f1e146098090a6b8e1b",
    "token": "bd9235029d8d9f74041175288d05dee60a2307eda6f895be59c995279848afef",
    "backstop": "4e1fc994e866939bcc46b6e100eaf7fc07cf6a675badd1a1767cc78351a05f80",
    "lendingPool": "0d992b1783d1c7045c1fe37065162cecead78c07c8c1900538ea98a5db3a338b",
    "oracle": "723523383575ffad246408a1d5674cbf97356b27fab5357c6e6d2081096b3cb2",
    "comet": "57bf92511d9a865501927f756b3a0970c714d0736f7912b296f776fca3b8b536"
  }
}
```

#### Step 4: Modify the deploy-pool.js script to set the pool parameters

```typescript
/// Deployment Constants
const deposit_asset = BigInt(2); // 0=BLND, 1=USDC, 2=Both
const blnd_max = BigInt(9_000_000e7);
const usdc_max = BigInt(100_000e7);
const mint_amount = BigInt(60_000e7);
const pool_name = "PairTrade";
const backstop_take_rate = 0.5e7;
const max_positions = 2;
const reserves = ["XLM", "wBTC"];
const reserve_configs: ReserveConfig[] = [
  {
    index: 0, // Does not matter
    decimals: 7,
    c_factor: 980_0000,
    l_factor: 980_0000,
    util: 900_0000, //must be under 950_0000
    max_util: 980_0000, //must be greater than util
    r_base: 100,
    r_one: 50_0000,
    r_two: 100_0000,
    r_three: 1_000_0000,
    reactivity: 1000, //must be 1000 or under
  },
  {
    index: 0,
    decimals: 7,
    c_factor: 980_0000,
    l_factor: 980_0000,
    util: 900_0000,
    max_util: 980_0000,
    r_base: 100,
    r_one: 50_0000,
    r_two: 100_0000,
    r_three: 1_000_0000,
    reactivity: 1000,
  },
];
const poolEmissionMetadata: ReserveEmissionMetadata[] = [
  {
    res_index: 0, // first reserve
    res_type: 1, // 0 for d_token 1 for b_token
    share: BigInt(0.5e7), // Share of total emissions
  },
  {
    res_index: 1, // second reserve
    res_type: 1, // 0 for d_token 1 for b_token
    share: BigInt(0.5e7), // Share of total emissions
  },
];
const startingStatus = 0; // 0 for active, 2 for admin on ice, 3 for on ice, 4 for admin frozen
const addToRewardZone = true;
const poolToRemove = "Stellar";
const revokeAdmin = true;
```

#### Step 5: Build Scripts

```bash
npm run build
```

#### Step 6: Deploy the Pool

```bash
node ./lib/scripts/user-scripts/deploy-pool.ts testnet
```
