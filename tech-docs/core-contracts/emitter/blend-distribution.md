# Blend Distribution

The Emitter contract is the admin of the Blend token and is responsible for minting and emitting Blend to the current backstop.

## Normal Emissions

Regular emissions are carried out via the `distribute()` emitter function. This function mints an amount of Blend equal to the number of seconds that have passed since the last distribution time and sends it to the backstop contract. The Backstop contract handles further distribution logic.

## Drop Emissions

Blend emissions are also carried out via "Drops". Drops are fixed 50 million token emissions that can be executed after every backstop swap. Drops are carried out by the Emitter contract's`drop()` function which  uses a `drop_recipient` list stored in the Backstop contract to determine what addressed Blend should be minted to.

{% hint style="warning" %}
Each Backstop contract address can only call `drop()` once. If the backstop contract address is swapped then swapped back the original backstop still will not be able to call drop again.
{% endhint %}

{% hint style="danger" %}
The listed Backstop contract must call `` drop()` `` so Backstop contracts must always have a method of doing so
{% endhint %}

Drops are intended to incentivize further open-source development of the protocol - as a backstop swap represents the release of a new protocol version.
