# Integrate with a Blend Pool

Table of Contents
* [Step 1: Choosing a Blend Pool](#step-1-choosing-a-blend-pool)
* [Step 2: Determine if an intermediate contract is necessary](#step-2-determine-if-an-intermediate-contract-is-necessary)
* [Step 3: Display Blend Pool data](#step-3-display-blend-pool-data)
* [Step 4: Allow users to supply and borrow assets](#step-4-allow-users-to-supply-and-borrow-assets)

## Step 1: Choosing a Blend Pool

The first step in creating a Blend Pool integration is to choose the Blend pool. This is the MOST important step. Blend pool's come in a variety of setups and support a large amount of assets and use cases, so we highly recommend reviewing the documentation on [choosing pools](../../users/choosing-pools.md).

## Step 2: Determine if an intermediate contract is necessary

The second step is deciding if your integration will be directly with the Blend pool or through an intermediate contract. We highly recommend reviewing the documentation for the [fee vault](./fee-vault.md) to see if the features it provides are worth it for your integration. The fee vault enables functionality like interest sharing to add an additional revenue stream for wallet providers.

If using any intermediate contract, the user will be interacting with the intermediate contract instead of the pool directly. The intermediate contract will then interact with the pool for the user.

## Step 3: Display Blend Pool data

This step will be broken up into a few parts, as data display requirements vary significantly for applications. To read data directly from the RPC without using the Blend SDK, you will need to simulate a transaction against the pool contract's getter functions. For more information about reading contract functions, please see the [Stellar dapp documentation](https://developers.stellar.org/docs/build/guides/dapps/frontend-guide).

### Pool Configuration

The `pool` configuration is where all data about the pool is stored. This includes the pool's name, supported reserves, oracle, and current take rate.

[SDK](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/pool_metadata.ts):
```javascript
import { PoolMetadata } from '@blend-capital/blend-sdk-js';

const network = {
  passphrase: "Test SDF Network ; September 2015",
  rpc: "https://horizon-testnet.stellar.org",
};
const poolId = "C...";
const poolMetadata = await PoolMetadata.load(network, poolId);
```

[Contract Function](https://github.com/blend-capital/blend-contracts-v2/blob/main/pool/src/contract.rs#L80-L88):
```rust
/// Fetch the pool configuration
fn get_config(e: Env) -> PoolConfig;

/// Fetch the admin address of the pool
fn get_admin(e: Env) -> Address;

/// Fetch the a vec addresses of all reserves in the pool. The index of the reserve
/// in this vec defines the index of the reserve in the pool, used in places like `Positions`.
fn get_reserve_list(e: Env) -> Vec<Address>;
```

### Reserve data

The `reserve` is where all data about the status of a given reserve is stored for a pool. This includes the total amount supplied, borrowed, the reserves configuration, index, interest rates, and emissions information.

If you support more than one reserve, it's recommended to load the entire pool at once, as this is done efficiently in the SDK. 

[SDK Load all reserves](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/pool.ts):
```javascript
import { ReserveV2, PoolV2 } from '@blend-capital/blend-sdk-js';

const network = {
  passphrase: "Test SDF Network ; September 2015",
  rpc: "https://horizon-testnet.stellar.org",
};

const poolId = "C..."; // The pool ID of the pool you want to load
const asssetId = "C..."; // The asset ID of the reserve you want to load

// If you are loading more than one asset, it's recommended to load the entire pool at once.
// This loads the poolMetadata and data for all reserves in the pool.
const pool = await PoolV2.load(network, poolId);
const reserve = pool.reserves.get(asssetId);

// ---- To load a single reserve ----

// The backstop take rate is the percentage of interest earned by the backstop module
// and defined within the pool configuration.
const backstopTakeRate = poolMetadata.backstopTakeRate;

// The reserve index is based on the order the reserve was added to the pool, and
// is also the same order as the reserve list in the pool metadata.
const reserveIndex = 0;
const reserveId = "C...";

const reserve = await ReserveV2.load(network, poolId, reserveIndex);
```

If you only support one reserve, you can load the reserve directly.

[SDK Load one reserves](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/reserve.ts):
```javascript
import { ReserveV2, PoolV2 } from '@blend-capital/blend-sdk-js';

const network = {
  passphrase: "Test SDF Network ; September 2015",
  rpc: "https://horizon-testnet.stellar.org",
};

const poolId = "C..."; // The pool ID of the pool you want to load
const asssetId = "C..."; // The asset ID of the reserve you want to load

const poolMetadata = await PoolMetadata.load(network, poolId);

// The backstop take rate is the percentage of interest earned by the backstop module
// and defined within the pool configuration.
const backstopTakeRate = poolMetadata.backstopTakeRate;

// The reserve index is based on the order the reserve was added to the pool, and
// is also the same order as the reserve list in the pool metadata.
const reserveIndex = poolMetadata.reserves.findIndex(
  (reserve) => reserve === asssetId
);

const reserve = await ReserveV2.load(network, poolId, reserveIndex);
```

If you are not using the SDK, there is a contract function to get the reserve data. However, you will need to calculate the interest rates seperately.

[Contract Function](https://github.com/blend-capital/blend-contracts-v2/blob/main/pool/src/contract.rs#L90-L94)
```rust
/// Fetch information about a reserve, updated to the current ledger
///
/// ### Arguments
/// * `asset` - The address of the reserve asset
fn get_reserve(e: Env, asset: Address) -> Reserve;
```

To calculate interest rates, you can use the `Reserve` struct returned from the `get_reserve` function and follow the interest formula.
* [Interest formula whitepaper](../../whitepaper/interest-formula.md)
* [Blend SDK interest rate calculation](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/reserve.ts#L351)


### Emissions data

Some reserve's earn BLND emissions, which can be emitted to the reserve's suppliers or borrowers. For more information about emissions, please see the [emissions documentation](../../emissions.md).

For the SDK, emissions data is included in when loading the reserve for both supply and borrow emissions, and will be undefined if no emissions exist for that position.

[Contract Function](https://github.com/blend-capital/blend-contracts-v2/blob/main/pool/src/contract.rs#L243-L251)
```rust
/// Get the emissions data for a reserve token
///
/// A reserve token id is a unique identifier for a position in a pool.
/// - For a reserve's dTokens (liabilities), reserve_token_id = reserve_index * 2
/// - For a reserve's bTokens (supply/collateral), reserve_token_id = reserve_index * 2 + 1
///
/// ### Arguments
/// * `reserve_token_id` - The reserve token id
fn get_reserve_emissions(e: Env, reserve_token_id: u32) -> Option<ReserveEmissionData>;
```

To calculate an APR for BLND emissions, some math is required.
1. Calculate the total amount of BLND emitted per protocol token [SDK Reference](https://github.com/blend-capital/blend-sdk-js/blob/main/src/emissions.ts#L59)
2. Determine the current price of BLND and the reserve token in the same unit (e.g. USDC). This requires converting 1 reserve token to it's underlying asset using the `d_rate` or `b_rate`, then applying either the oracle price of the reserve token or another, equivalent price.
3. Calculate an APR [Blend UI Reference](https://github.com/blend-capital/blend-ui/blob/main/src/utils/math.ts#L10-L26)

### User data

User data is stored as a Map of `positions` of protocol tokens. The reserve index is the key for the map, and the corresponding amount of `bTokens` or `dTokens` is the value. Positions can be one of three types - Supply, Collateral, or Liabilities. It's most common for user's to `SupplyCollateral`, so most supplied users balances will be stored in `Collateral`.

Generally, it's best to display the underlying value of a user's position, rather than the protocol token amount. The SDK contains helper functions on the `Reserve` class to convert between the two, and we also recommend referring to the [Protocol Token documentation](../core-contracts/lending-pool/protocol-tokens.md).

[SDK](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/pool_user.ts)
```javascript
import { PoolUser, PoolV2 } from '@blend-capital/blend-sdk-js';

const network = {
  passphrase: "Test SDF Network ; September 2015",
  rpc: "https://horizon-testnet.stellar.org",
};
const poolId = "C..."; // The pool ID of the pool you want to load
const userId = "G..."; // The pubkey/contract address of the user you want to load
const pool = await PoolV2.load(network, poolId);
const user = await pool.loadUser(userId);
```

If you opted to load only a single reserve, you can still load user data.

[SDK](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/user_types.ts)
```javascript
import { Positions, PoolUserEmissionData } from '@blend-capital/blend-sdk-js';

const network = {
  passphrase: "Test SDF Network ; September 2015",
  rpc: "https://horizon-testnet.stellar.org",
};
const poolId = "C..."; // The pool ID of the pool you want to load
const userId = "G..."; // The pubkey/contract address of the user you want to load

const reserve = // ... see above to load the reserve
const reserveTokenIndex = 1; // The reserve token index used for emissions

const positions = await Positions.load(network, poolId, userId);
const userEmissionData = await PoolUserEmissionData.load(network, poolId, userId);
```

[Contract Function](https://github.com/blend-capital/blend-contracts-v2/blob/main/pool/src/contract.rs#L243-L251)
```rust
/// Fetch the positions for an address. For each position type, there is a map of the reserve index
/// to the position for that reserve, if it exists.
///
/// ### Arguments
/// * `address` - The address to fetch positions for
fn get_positions(e: Env, address: Address) -> Positions;

/// Get the emissions data for a user
///
/// A reserve token id is a unique identifier for a position in a pool.
/// - For a reserve's dTokens (liabilities), reserve_token_id = reserve_index * 2
/// - For a reserve's bTokens (supply/collateral), reserve_token_id = reserve_index * 2 + 1
///
/// ### Arguments
/// * `user` - The address of the user
/// * `reserve_token_id` - The reserve token id
fn get_user_emissions(e: Env, user: Address, reserve_token_id: u32)
    -> Option<UserEmissionData>;
```

Note that the values in `Positions` will need to be fetched with the reserve index, and converted from it's protocol token form to the underlying asset using the `d_rate` or `b_rate` of the reserve.

## Step 4: Add Supply and Withdraw functionality

To allow users to supply assets to the pool, you can submit a transaction with a `SupplyCollateral/WithdrawCollateral` request to the pool's `submit` or `submit_with_allowance` function. `SupplyCollateral/WithdrawCollateral` is generally recommended for most users over `Supply/Withdraw`, as it allows the user to borrow against their supplied assets if they choose to do so. Using `submit` will include an authorization request for the user to transfer the requested amount of tokens within the transaction, and using `submit_with_allowance` will require the user to approve the pool contract to transfer the requested amount of tokens before submitting the transaction. Most integrations will use `submit` unless they have a specific reason to use `submit_with_allowance`.

To review how to submit a Soroban operation on stellar, please see the [Soroban documentation](https://developers.stellar.org/docs/build/guides/transactions/simulateTransaction-Deep-Dive#overview).

[SDK](https://github.com/blend-capital/blend-sdk-js/blob/main/src/pool/pool_contract.ts#L210-L228)
```javascript
import { PoolContract, RequestType } from '@blend-capital/blend-sdk';
import { xdr } from '@stellar/stellar-sdk';

const asset = "C..."; // The contract address of the reserve you want to supply
const user = "G..."; // The pubkey/contract address of the user supplying the asset

// The amount of asset to lend as a fixed point number with the assets decimal places.
// (e.g. 1.0 XLM = 10000000n)
const to_lend: 1000n; 

// The amount of asset to withdraw, as a fixed point number with the assets decimal places. If the amount of
// assets to withdraw is greater than the value of the user's bTokens, the transaction will pull down the withdraw amount
// to the user's bToken posiiton balance.
// (e.g. 1.0 XLM = 10000000n)
const to_withdraw: 1000n;

const pool_contract = new PoolContract(poolId);
const supply_op = xdr.Operation.fromXDR(
    pool_contract.submit({
        from: user,
        spender: user,
        to: user,
        requests: [
            {
                amount: to_lend,
                request_type: RequestType.SupplyCollateral,
                address: asset,
            },
        ],
    }),
    'base64'
);
const withdraw_op = xdr.Operation.fromXDR(
    pool_contract.submit({
        from: user,
        spender: user,
        to: user,
        requests: [
            {
                amount: to_withdraw,
                request_type: RequestType.WithdrawCollateral,
                address: asset,
            },
        ],
    }),
    'base64'
);

// simulate, assemble, and submit the transaction
```

