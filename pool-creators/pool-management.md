# Pool Management

Pool management encompasses modifying pool status to freeze or unfreeze pools — and modifying pool parameters to add assets or adjust their risk/interest rate parameters.

### Pool Admin

Pools may set a designated admin that has the authority to change pool status or update asset risk/interest rate parameters.

Pools with admins are considered owned pools. Alternatively, the pool's admin address can be set to a dead address, which makes the pool immutable, although its status can still be changed by the backstop module.

Pools without admins are standard pools. Standard pools are more decentralized and trustless than owned pools but lack the flexibility some pool creators may desire. We should note that an admin address must still be supplied to create and set up a pool. After the pool is created, the admin should change the admin address to a dead address to make the pool standard.\
\
Pool creators set the pool admin when they create the pool using the `deploy_pool` function on the pool factory contract. They can change it later using the `set_admin` function.

### Pool Status

Pool status changes are the primary way that pools can respond to unforeseen risks. Pools have three statuses.

#### Active:

Active pools function normally.

#### On-Ice:

Pools that are On-Ice have borrowing disabled. This status is designed for when the pool is at risk but not defunct. For example, if the pool's oracle suffers liveness issues that will be resolved, the pool should be set to On-Ice.

#### Frozen:

Frozen pools have borrowing and depositing disabled. This status is for when a pool is defunct and should no longer be used. For example, suppose backstop module depositors in a standard pool want to move liquidity to a new pool with updated assets and parameters. In that case, they should coordinate to freeze the old pool.

The pool status can be changed at any time by the pool manager or, if the correct backstop state is met, by anyone.

### Backstop State Requirements:

The backstop requirements to set specific pool status are as follows:

#### Active:

* Backstop deposits must exceed the minimum backstop requirement

\-and-

* Less than 50% of backstop deposits are queued for withdrawal.

#### On-Ice:

* Backstop deposit fall below the minimum deposit requirement

\-or-

* Over 50% of backstop deposits are queued for withdrawal.

#### Frozen:

* Over 75% of backstop deposits are queued for withdrawal.

When a pool admin sets the pool to a more restrictive status, the backstop state cannot be used to set a less restrictive state. Likewise, pool admins cannot set a less restrictive status if the pool's backstop's state does not meet the requirements for that status.

### Initial Pool Status

Pools start in the Setup status which disallows all pool actions. This is to allow the to finalize setup by adding assets to the pool before user's begin using it. After setup is finished the admin can use the `set_status()` function to set the status to On-Ice, allowing user's to deposit but not borrow. Pool creators should note that after they move the pool out of the Setup status there will be a mandatory 7 day queue to modify asset parameters or add new assets. This is to prevent pool admins from changing asset parameters or adding new assets without giving users a chance to exit the pool or backstops a chance to queue withdrawals.

Admin's are not immediately able to set pool's to active to limit the creation of unsafe pools by requiring that some insurance capital be put forward before a pool can be used. The rationale here is high-risk pools are unlikely to have many people willing to insure them.

### Pool Migration

Standard pools occasionally need to be updated when oracles need to be changed or when asset parameters require an update. Backstop-triggered status changes allow backstop depositors to force borrowers and lenders to move to a new pool. When an updated pool is deployed, backstop depositors can coordinate to queue their deposits for withdrawal; then anyone can update pool status to FROZEN — forcing lenders and borrowers to migrate.\
\
Owned pools also need to be updated when oracles need to be changed since pool admins cannot update the oracle address. Pool migration is simpler for owned pools as the admin can set the pool status to FROZEN without coordinating with backstop depositors.

### Risk/Interest Rate Parameters

Pool admins can modify asset risk and interest rate parameters and add or remove assets from the pool. If the pool is a standard pool and does not have an admin, asset parameters require a pool migration to update.
