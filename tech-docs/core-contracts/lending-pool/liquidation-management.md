# Liquidation Management

The pool contract is used to create and carry out user liquidations which are carried out via auctions.

## Creating Liquidations

Liquidations are created with the `create_user_liquidations()` function. This function creates a liquidation auction which is used to auction off a portion of a user's collateral in exchange for the liquidator assuming a portion of the user's debt.

The creator of the liquidation auction can choose what percent of the user's positions to put up for auction, however, the percent chosen must result in the user's account having a health factor between 1.03 and 1.15 after the liquidation is fully filled.

All a user's positions are involved in every auction - the percentage of each position that is transferred to the liquidator is determined by the number of blocks that have passed since the liquidation started. The percentage of collateral positions transferred increases by .05% every block for the first 200 blocks - and the percentage of debt positions transferred decreases by .05% every block for the second 200 blocks.

## Filling Liquidations

Liquidations are filled using a `Request` struct input into the `submit()` function, which is shared by user fund management. When the request is handled, a portion of the delinquent user's positions are added to the liquidator's positions. The liquidator can atomically manipulate these positions with additional `Request` structs they bundled with the `fill_liquidation` `Request` (ie. adding a `repay` or `deposit_collateral` request after the liquidation is filled).&#x20;

{% hint style="info" %}
The typical `submit()` health factor check ensures that the liquidator either has enough collateral or has repaid enough debt to complete the liquidation fill
{% endhint %}

Filling liquidations this way is more gas-efficient and capital-efficient than normal liquidation mechanisms. It also allows liquidators to delay repaying the user's delinquent debt by ensuring their account has enough collateral to cover the additional debt. Supporting these kinds of fills lowers the risk of liquidation cascades and also allows liquidators to slowly unwind positions rather than having to repay them immediately which lowers capital requirements.

Liquidators can fill liquidations fully or partially by inputting the percentage of the liquidation they want to fill in the `amount` field of the `Request` struct.&#x20;

## Cancelling Liquidations

If at any point during a liquidation auction, the delinquent user's account becomes healthy - the user can cancel the liquidation auction by calling the `submit()` function with a `cancel_liquidation_auction` `Request` . Liquidations typically take \~150 blocks to fill so this is especially useful if a user realizes they're being liquidated and wants to collateralize their account AND cancel the ongoing liquidation by submitting a `repay` or `deposit_collateral` `Request` bundled with a `cancel_liquidation_auction` `Request`.
