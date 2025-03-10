# Deposit Management

The Backstop contract allows users to deposit backstop tokens and allocate them towards different pools.

## Storing User Backstop Deposits

Users backstop deposits are allocated to individual pools. Their deposits are stored by the Backstop contract in a hashmap where the key is a `PoolUserKey` struct

```rust
pub struct PoolUserKey {
    pool: Address, // Pool Contract Address
    user: Address, // User Account Address
}
```

and the value is a `UserBalance` struct

```rust
pub struct UserBalance {
    pub shares: i128,  // the balance of shares the user owns
    pub q4w: Vec<Q4W>, // a list of queued withdrawals
}
```

## Deposits

Users deposit backstop tokens into the backstop contract using the `deposit()` function which accepts a deposit amount and a pool address. The specified amount will be sent to the backstop contract, and recorded as a user's deposit in the specified pool. The deposit amount is accounted for as a number of shares, calculated as:

$$
SharesIssued = DepositAmount * \frac{TotalShares}{TotalTokens}
$$

The number of backstop tokens each share is worth fluctuates based on the behavior of the pool the deposit is allocated to

- Share value increases when the associated pool pays interest to the backstop as this increases the pool's `total_tokens`&#x20;
- Share value decreases when deposits are used to cover bad debt as this decreases the pool's `total_tokens`&#x20;

Once the deposit is finalized the pool's `total_tokens` will be increased by the deposit amount and the pool's `total_shares` will be increased by the number of shares issued.

{% hint style="warning" %}
`deposit()` calls will fail if the user attempts to deposit into a pool not listed as a `deployed_pool` by the designated Pool Factory contract
{% endhint %}

## Withdrawals

Backstop withdrawals are handled in two steps.

1. The user calls `queue_withdrawal` with the amount they wish to withdraw and the pool they wish to withdraw from. This will queue a withdrawal for the specified amount of tokens from the specified pool.&#x20;

{% hint style="warning" %}
While deposits are queued for withdrawal the user will no longer receive emissions from the backstop. They will still receive their share of interest from the pool.
{% endhint %}

1. User waits for the 17-day withdrawal queue to expire

{% hint style="info" %}
At any point, the user can cancel a queued withdrawal using the `cancel_withdrawal()` function.
{% endhint %}

{% hint style="info" %}
A withdrawal queue is required to prevent backstop depositors from leaving a pool before their backstop deposits can be used to cover bad debt.
{% endhint %}

3. User calls `withdraw()` to withdraw the shares. This will transfer the tokens to the user and reduce the pool `total_tokens` and `total_shares` accordingly.
   1. Tokens received are calculated as:

$$TokensReceived = SharesWithdrwawn * \frac{TotalTokens}{TotalShares}$$
