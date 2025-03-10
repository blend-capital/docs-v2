# Backstop Bootstrapping

It can be difficult to source either the USDC or BLND to successfully reach the required threshold of a new pool's backstop. To help with this an open source smart contract called the backstop bootstrapper has been released.\
[https://github.com/blend-capital/backstop-bootstrapper](https://github.com/blend-capital/backstop-bootstrapper)

### What the heck is a bootstrap?

The backstop bootstrapper allows anyone to create a "Bootstrap" event which allows them to put up a given amount of BLND or USDC, then allows other users called joiners to put up the other token throughout the event. At the end of the event all tokens are deposited into the BLND:USDC liquidity pool and the acquired liquidity pool tokens are distributed to the bootstrap initiator and the joiners.&#x20;

This event serves as an "auction" that enables the bootstrap initiator to acquire liquidity pool tokens at a much better price than they would just depositing into the liquidity pool if they are trying to deposit a large amount. Put in story language, a bootstrap goes as following\
\
`Bill Bootstrapper: Arrrrgh, shiver me timbers, I've got 150,000 o' this here BLND I'm lookin' ta deposit in the liquidity pool o' Comet. Any of you scalleywags brave enough ta' put some USDC into this deposit so we get a better bounty?`

`Sammy Scalleywag: Ahoy Capin', here's 5,000 o' my hard earned USDC i'll deposit alongside ye so you get a bargin from the liquidity pool.`

`Tommy Twofingers: Now don't you two rapscallions go leavin' out tommy. Here's 10,000 of my plundered USDC for ye ta put into that liquidity pool.`

`Bill Bootstrapper: Thanks mateys, I made the deposit and we got 30% more liquidity pool tokens than we woulda gotten if we ad made em seperately. Here's yer share and the grogs on me tonight!`

### Overview

This smart contract allows anyone to create a new Bootstrap event by depositing a given amount of BLND or USDC

Then other users can deposit the pair token (BLND if the bootstrap token is USDC, and USDC if the bootstrap token is BLND). They can withdraw these tokens at any point until the bootstrap event ends. \
\
Once the bootstrap event is completed, if the minimum number of pair tokens has been reached, the backstop and pair tokens are deposited into the BLND:USDC comet liquidity pool.

The BLND depositors (or the bootstrapper) are allocated 80% of the received comet liquidity pool tokens. And the USDC depositors (or the bootstrapper) are allocated 20% of the received liquidity pool tokens.&#x20;

The bootstrapper receives all of their allotted tokens. The pair token depositors (joiners) receive their proportional share of liquidity pool tokens.

EX: Say the pair token was USDC,  100 USDC was deposited, and 1000 liquidity pool tokens were received. A pair token depositor who deposited 10 USDC would receive 20 liquidity pool tokens (1000\*20\*100 = 20)

### Pricing Bootstrap Tokens

We will here provide an example for how to easily price bootstrap tokens as a pair token depositor

The price of a bootstrap token is roughly equal to&#x20;

$$
bootstrapTokenPrice \approx\ \frac{backstopTokenWeight}{pairTokenWeight}\ *\frac{totalPairTokens}{totalBootstrapTokens}\
$$

So if the bootstrap token is BLND (weight 80%) and there is 160 BLND deposited in the bootstrap and 20 USDC, the implied price of BLND is 0.5$ (.8/.2\*20/160 = .5)

Thus, as a pair token depositor, if your deposit will bring the price of the bootstrap token above the price of the bootstrap token if purchased from the liquidity pool, it's better to buy the bootstrap token from the liquidity pool than participate in the bootstrapping event.

{% hint style="info" %}
This is a rough pricing method - when bootstrap and pair tokens are deposited into the blend liquidity pool if their ratio is not equal to the ratio of bootstrap and pair tokens in the liquidity pool, the deposit will incur trading fees and slippage. Thus when calculating the bootstrap token price with the simplified method you should assume the real price is slightly higher than what you calculated.
{% endhint %}

