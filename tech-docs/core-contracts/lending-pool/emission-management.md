## Emission Management

The pool contract is used to distribute the pool's share of emissions to lenders and borrowers and to facilitate user claims.

### Distributing Emissions

Pool's must be in the reward zone to receive emissions. If they are in the reward zone, emissions are distributed to lenders and borrowers based on the pool's emission configuration. The emission configuration is set by the pool's owner and can be updated at any time. The emission configuration specifies the percentage of emissions that should be distributed to lenders and borrowers of different assets.

Distribution is carried out through 3 steps:

1. `distribute()` is called on the emitter, which sends BLND to the backstop.
2. `gulp_emissions()` is called on the backstop, which as well as handling BLND distribution to the pool's backstop depositor, allocates a portion of BLND emissions to the pool.
3. The pool's `gulp_emissions()` function is called this function:
   - Calls `gulp_pool_emissions()` on the backstop - approving approving the pool to transfer it's allocated BLND from the backstop to the pool.
   - Sets EPS for every pool reserve.

### Claiming Emissions

Users can claim their share of emissions by calling `claim_emissions()` and inputting the reserves that they want to claim emissions for.
