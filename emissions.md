# Emissions

### What are emissions in Blend?

The Blend protocol emits 1 BLND per second that get distributed to both backstop users and pool users. The tokens get emitted by the Emitter contract, which sends tokens to the current backstop contract. The destination of emissions can be modified through a [backstop swap](tech-docs/core-contracts/emitter/backstop-management.md).&#x20;

The backstop contract manages how emissions get distributed throughout the protocol. The backstop allocates the incoming BLND from the `emitter` to the reward zone pools with the `distribute()` function. Pools then can run `gulp_emissions()` to claim these allocated BLND tokens and setup emissions for both it's backstop depositors and pool users.

### How do backstop emissions work?

Backstop emissions are earned for having tokens deposited into a reward zone pool's backstop. Any tokens that are queued for withdraw are NOT eligible for emissions.

During claim, the BLND tokens earned are used to mint additional backstop tokens, and automatically deposited into the pool's backstop for the user.

For more information on how emissions are tracked in the backstop, see the[ tech docs](tech-docs/core-contracts/backstop/emission-distribution.md).

### How do pool emissions work?

The pool admin can set up which positions earn emissions, and how much of the total share of emissions they receive (e.g. supplying XLM or borrowing USDC). Any user that maintains one of those positions while emissions are ongoing will accrue BLND.

During claim, the accrued BLND is sent directly to the user.

For more information on how emissions are tracked in the pool, see the [tech docs](tech-docs/core-contracts/lending-pool/emission-management.md).

### Backfilled emissions

Blend v2 comes with an additional feature that applies during the migration period of Blend v1 to Blend v2, or rather, before the backstop swap to Blend v2 is fully complete.

During this time, the Emitter is sending 1 BLND per second to Blend v1 only, and no tokens are being emitted to Blend v2.

Blend v2 will still track and accrue emissions for users at a rate of 1 BLND per second, for up to 10 million BLND, in backfilled emissions. These emissions CANNOT be claimed until a backstop swap successfully occurs. If a backstop swap to Blend v2 occurs, the Blend v2 backstop is now eligible for a `drop` from the Emitter. This is a distribution of up to 50 million BLND tokens. The Blend v2 backstop will include all backfilled emissions to be dropped to itself, when `drop` is invoked.

{% hint style="warning" %}
Backfilled emissions CANNOT be claimed unless the backstop swap to Blend v2 is succesfull!
{% endhint %}

At this point, the Blend v2 backstop has all BLND emitted during the backfilled emissions period, and all claims can be processed. Further, the emitter will now be distributing tokens to the Blend v2 backstop instead of Blend v1. This means emissions will proceed as normal for Blend v2, and stop for Blend v1.&#x20;
