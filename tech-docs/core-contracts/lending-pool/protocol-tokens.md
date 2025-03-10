# Protocol Tokens

The Blend protocol represents all positions for a pool with tokens. This allows the pool to track interest fairly across multiple suppliers and borrowers for a given reserve. All protocol tokens are non-transferable, and are only an accounting method to safely represent positions.

When reading positions for an address via the `get_positions(address: Address)` function, the value returned is a Map of the reserve's index to the BToken or DToken balance.

## bTokens

A bToken represents a supply made to a Blend pool. Each reserve has a unique bToken per pool, and they are non-transferable.

bTokens are converted to and from underlying assets with the reserve's `bRate`. The `bRate` is adjusted when interest is accrued.

$$
bTokens = \frac{underlying}{bRate}
$$

$$
underlying = bRate*bRokens
$$

## dTokens

A dToken represents a liability against a Blend pool. Each reserve has a unique dToken per pool, and they are non-transferable.

dTokens are converted to and from underlying assets with the reserve's `dRate`. The `dRate` is adjusted when interest is accrued.

$$
dTokens = \frac{underlying}{dRate}
$$

$$
underlying = dRate*dTokens
$$
