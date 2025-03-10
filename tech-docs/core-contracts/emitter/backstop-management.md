# Backstop Management

The Emitter contract defines the backstop contract address that will receive blend tokens. It can switch this address through an emissions swap. This allows the Backstop contract to be "upgraded" while retaining its immutability.

## Setting the Initial Backstop

The Backstop contract address is initially set on Emitter initialization. This is done through the `initialize` function. This function can only be called once - any repeat calls will fail.

## Backstop Swaps

Backstop swaps can be carried out at any point if a new smart contract (generally an upgraded Backstop contract) contains more `backstop_tokens` than the current backstop contract. Backstop swaps can be used to upgrade both the Backstop contract and the Backstop Token contract.

The steps to undertake a swap are to:

1. Deploy a new Backstop contract
2. Convince existing backstop depositors that it is worthwhile to migrate to the new contract.

{% hint style="danger" %}
If the new Backstop contract is using a new backstop token, it must still be able to handle deposits of the current backstop token (so it can compare balances) or the swap will be impossible.
{% endhint %}

{% hint style="warning" %}
This may disrupt pools as backstop depositors must queue withdrawals from the old Backstop contract to deposit in the new Backstop contract. This could trigger pool status changes, which pool creators should be aware of.
{% endhint %}

3. Once the new contract has more backstop tokens than the current contract, call `queue_backstop_swap()` on the emitter contract with the new backstop contract address and the new backstop token address.

{% hint style="danger" %}
`queue_backstop_swap()` will fail if the new backstop contract does not have more backstop tokens than the current backstop contract.
{% endhint %}

{% hint style="info" %}
Only one backstop swap can be queued at a time. If a swap is already queued, the `queue_backstop_swap()` will fail.
{% endhint %}

4. Wait until the unlock time has passed (takes 31 days).

{% hint style="warning" %}
If at any point during the swap process, the new Backstop contract has fewer backstop tokens than the current Backstop contract, the swap can be canceled by calling `cancel_backstop_swap` on the Emitter contract.
{% endhint %}

{% hint style="info" %}
A 31 day queue time is to prevent malicious actors from swapping the Backstop contract without giving backstop depositors time to withdraw the LP positions (backstop tokens are liquidity pool tokens) and force the attacker to finalize their trade.
{% endhint %}

5. Call `swap_backstop` on the Emitter contract.

{% hint style="danger" %}
If the new Backstop contract no longer has more backstop tokens than the original the call will fail
{% endhint %}

{% hint style="info" %}
A final `distribute()` call will be made to the old backstop contract before the swap is executed to ensure no emissions are lost.
{% endhint %}

The swap is now complete. The old backstop contract will continue functioning as expected. However, backstop depositors in the old contract and users of pools associated with that backstop will no longer receive emissions - prompting them to switch to the new contracts.
