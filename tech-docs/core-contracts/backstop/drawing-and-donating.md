# Drawing and Donating

Pools interact with backstop deposits allocated to them by drawing from the deposits to cover bad debt and donating a percentage of interest to the deposits.

## Storing Pool Backstop Deposits

The Backstop contract stores total backstop deposits allocated to each pool contract in a hashmap where the key is the Pool contract address and the value is a `PoolBalance` struct

```rust
pub struct PoolBalance {
    pub shares: i128, // the amount of shares the pool has issued
    pub tokens: i128, // the number of backstop tokens the pool holds in the backstop
    pub q4w: i128,    // the number of shares queued for withdrawal
}
```

## Drawing

Pools draw from backstop deposits allocated to them using the `draw()` function. This transfers the requested amount of backstop tokens from a specified pool's backstop deposits to a specified recipient and reduces the pool's `total_tokens` by the amount drawn.

{% hint style="info" %}
Reducing the pool's `total_tokens`lowers the value of backstop deposits allocated to that pool
{% endhint %}

{% hint style="danger" %}
The draw function must be called by the specified pool or it will fail.
{% endhint %}

This function is used to allow pools to cover the bad debt they accumulate.

## Donating

Any party can donate to backstop depositors using the `donate()` function. This transfers the specified amount of backstop tokens from the caller to the backstop contract and increases the specified pool's `total_tokens`.

{% hint style="info" %}
Increasing a pools `total_tokens` increases the value of backstop deposits allocated to that pool
{% endhint %}

This function is used to allow pools to donate a percentage of interest to backstop depositors.
