# Fund Management

The Lending pool contract allows users and liquidators to manipulate the user funds it stores.

## Requests

All fund management is carried out using `Request` structs&#x20;

```
pub struct Request {
    pub request_type: u32,
    pub address: Address, // asset or liquidatee address
    pub amount: i128,
}
```

which are input into a single `submit()` function. Multiple requests can be bundled together to carry out actions atomically (e.g. supply and borrow in the same transaction). The `submit()` function will simply revert if the user's account is unhealthy after all requests are processed.

The following request types are supported:

* `Deposit` (enum 0): Deposits funds into the pool. These funds are not collateralized.&#x20;

{% hint style="info" %}
This request is useful for users who want to deposit funds but do not want these funds to be liquidated in the event their account becomes delinquent. Additionally, they're valuable in pools with strict position count limits since uncollateralized deposits don't count toward position count limits.
{% endhint %}

{% hint style="danger" %}
Deposit requests will fail if the pool status is greater than 3 (this means the pool is `Frozen`)
{% endhint %}

* `Withdraw` (enum 1): Withdraws uncollateralized funds from the pool.
* `Deposit Collateral` (enum 2): Deposits collateral into the pool.

{% hint style="danger" %}
Deposit Collateral requests will fail if the pool status is greater than 3 (this means the pool is `Frozen`)
{% endhint %}

* `Withdraw Collateral` (enum 3): Withdraws collateral from the pool.
* `Borrow` (enum 4): Borrows funds from the pool.

{% hint style="danger" %}
Borrow requests will fail if `pool_status` is greater than 1 (meaning the pool is `Frozen` or `On-Ice`)
{% endhint %}

* `Repay` (enum 5): Repays borrowed funds.
* `Fill Liquidation` (enum 6): Fills a user liquidation. This involves transferring a portion of the liquidated user's collateral and liabilities to the liquidator.
* `Fill Bad Debt Auction` (enum 7): Fills a bad debt auction. This involves transferring bad debt stored as liabilities in the Backstop contract's positions to the liquidator's positions.
* `Fill Interest Auction` (enum 8): Fills an interest auction. This request does not modify the filler's positions.
* `Delete Liquidation Auction` (enum 9): Cancel's an ongoing liquidation.&#x20;

{% hint style="danger" %}
Delete Liquidation Auction requests will fail if `pool_status` is greater than 1 (meaning the pool is `Frozen` or `On-Ice`)
{% endhint %}

{% hint style="danger" %}
All requests will fail if the pool status is 6 (`Setup)`
{% endhint %}

Requests are flexible in that they can be carried out on behalf of other users utilizing the `spender` `from` and `to` parameters on the `submit()` function. This allows users to delegate fund management to other users or contracts.

{% hint style="warning" %}
The addresses input into the `from` and `to` parameters are required to authorize the `submit()` call or it will fail.&#x20;
{% endhint %}
