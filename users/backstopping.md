# Backstopping

### What is a Backstopping?

Each Blend lending pool has a backstop module, which protects the lending pool from bad debt. If a user incurs bad debt because their positions aren't liquidated quickly enough, their bad debt is transferred to the backstop module, which pays it off by auctioning off its deposits.\
\
Users can backstop pools by depositing into their backstop module, thus participating in insuring the lending pool.

### What is the Backstop Token?

The backstop supports a single token for all deposits.

The backstop token is [BLND:USDC 80:20 liquidity pool shares](backstopping.md#what-are-blnd-usdc-80-20-liquidity-pool-shares).

The contract address on mainnet can be found [here](../mainnet-deployments.md#cas3fl6tlzkdggsisdbwggpxt3nrr4dytzd7yod3hmyo6ltjuvgrveam).

### Why Would a User Backstop a Pool?

Backstop module depositors receive a percent of the interest pool borrowers pay in exchange for insuring pools. The percentage they are paid depends on a Backstop Take Rate, which is set on pool creation. So if a backstop take rate is 25%, backstop module depositors will receive 25% of all interest paid by borrowers from that pool. This interest is transferred to backstop module depositors as [BLND:USDC 80:20 liquidity pool shares.](backstopping.md#what-are-blnd-usdc-80-20-liquidity-pool-shares)

In addition, pools require a minimum amount of backstop deposits to be activated. Users who wish to see a certain pool activated may choose to backstop it to help it reach this minimum.

Finally, if the selected lending pool backstop is in the reward zone, backstop module depositors receive BLND emissions. This is to allow them to continue curating and insuring more pools in the Blend protocol.

### What are BLND:USDC 80:20 Liquidity Pool Shares?

Tokens representing ownership in a weighted liquidity pool (LP) holding BLND and USDC tokens in weights of 80% BLND and 20% USDC. \
\
A LP is a smart contract that functions as an exchange for the tokens it holds. Liquidity providers deposit tokens in the LP smart contract in exchange for shares representing their deposit. The contract utilizes an algorithm to allow traders to swap between the tokens it holds while paying fees to the liquidity providers.\
\
A weighted LP is a LP that has unequal values of the tokens it holds. Traditional LPs hold equal values of the tokens they hold. However, weighted ones can support any value distribution. A 80:20 LP will hold 80% of its value in token A and 20% in token B (A and B being BLND and USDC in our case).

### How do User's Backstop Pools?

Users can backstop pools by depositing [BLND:USDC 80:20 weighted liquidity pool shares](backstopping.md#what-are-blnd-usdc-80-20-liquidity-pool-shares) in the pools backstop module. They should note that if they wish to withdraw these deposits, they will have to queue them for withdrawal and wait for a 21-day withdrawal queue period to end.&#x20;

### What are the Risks of Backstopping?

Backstop depositors act as first-loss capital for lending pools. If the pool incurs bad debt, backstop deposits will be auctioned off to cover it. Losses incurred by covering bad debt are shared proportionally between backstop module depositors.&#x20;

### How do Backstoppers Claim Earned Interest and Emissions?

Pool backstoppers can claim earned interest and emissions at any point. When claimed, they are deposited into the [BLND:USDC 80:20 liquidity pool](backstopping.md#what-are-blnd-usdc-80-20-liquidity-pool-shares), and the shares are added to the user's backstop module deposits.&#x20;
