# Bad Debt Management

The Lending pool contract is responsible for assigning bad debt to the backstop - auctioning it off - and socializing it if it cannot be auctioned off.

## Bad Debt Assignment

Bad debt is assigned to the backstop when a user's account no longer has any more collateral.

When a user's account is fully liquidated, if they do not have enough collateral a liquidator may only repay a portion of their debt. After this - the user's account will only have debt and no collateral. Then, anyone can call the `bad_debt()` function to transfer all debt from the delinquent user to the backstop. The bad debt is transferred by adding it as debt positions to the backstop's pool positions.

## Bad Debt Auctions

Bad debt auctions are used to auction off bad debt to liquidators in exchange for a portion of the backstop deposits. \
They are initiated using the `create_bad_debt_auction` function. When this function is called, the auction creator will specify which debt positions they want to auction off. \
\
Bad debt auctions are filled by calling the `submit()` function with a `fill_bad_debt_auction` `Request`struct. When they are filled all the bad debt positions that have been added to the backstop's pool positions will be transferred to the fillers positions.

## Bad Debt Socialization

When bad debt auctions are filled - if the backstop no longer has over 5% of the backstop threshold but still has bad debt positions - the backstop's remaining debt positions are burned which socializes the bad debt, reducing the value of all deposits of this asset in the pool.
