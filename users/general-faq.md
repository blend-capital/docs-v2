# General/FAQ

## General

### What is Blend?

Blend is a DeFi (decentralized finance) protocol that allows any entity to create or utilize an immutable lending market that fits its needs.

### What is a Decentralized Finance protocol?

A decentralized finance protocol is a financial application that does not rely on or require its users to trust a central intermediary. This is achieved by building the protocol using immutable smart contracts that run on a distributed network. Blend consists of a group of immutable smart contracts running on Stellar's Soroban smart contract engine. No central organization controls Blend, and there is no organization Blend relies on to continue operating. This means that Blend is non-custodial, trust-minimized, and censorship-resistant.

### Why should you care about Blend?

Blend offers lending pools that are secure, capital efficient, and permissionless. Utilizing it, ecosystem entities will be able to provide a variety of new functionality to the Stellar ecosystem — such as real-world financing, leveraged trading, yield products, and new DeFi apps.

**Security**

All Blend lending pools are isolated from one another. Therefore, lenders and borrowers are only exposed to the risk of the pool they're using. Additionally, each lending pool has mandatory insurance through the [backstop module](../blend-whitepaper.md#backstop-module).

**Capital Efficiency**

Blend's reactive interest rate mechanism ensures there isn't slack capital in the protocol. Lenders and borrowers can expect to experience relatively efficient rates while using Blend.

**Permissionless**

Anyone can use Blend to lend or borrow. But more importantly, anyone can deploy a new lending pool as long as they can attract backstop module depositors. This means all valuable use cases for Blend should be quickly supported rather than having to go through an arduous governance process.

### What are the benefits of Blend for Stellar?

Blend brings decentralized, on-chain lending markets to the Stellar ecosystem.\
Within the ecosystem, this promises to:

* Increase trading and payment liquidity
* Improve capital productivity
* Reduce reliance on lending intermediaries like banks
* Offer new financial services to a global market

### How do I use Blend?

Currently, Blend is still in development. Once it's released to the Stellar testnet, there will be a web app where users can use it.&#x20;

More technical users can also use Blend directly by calling the smart contracts using the Horizon API layer. Please see the Soroban documentation and the [technical docs](../tech-docs/general.md) section for more information.

### Does Blend have fees?

Blend users can incur three possible fees:

**Network Fees**

Blend users must pay [Stellar Network fees](https://developers.stellar.org/docs/glossary/fees/) for each transaction.

**Interest Rates**

Borrowers on Blend must pay interest fees to lenders. These vary based on the parameters set by pool creators and fluctuate based on demand. For more information, see the [interest rates section](../blend-whitepaper.md#interest-rates) of the whitepaper.

**Liquidation Premiums**

When a Blend borrower is liquidated, the liquidator will likely demand a liquidation premium, meaning the value of the collateral they claim will be greater than the value of the liabilities they repay. The size of the premium is market-driven; it will always be the smallest premium needed to clear the liquidation. To learn more about the liquidation mechanism, see the[ liquidations section](../blend-whitepaper.md#liquidations) of the whitepaper.

### Can Blend be changed or upgraded?

No. The closest thing to an upgrade for Blend is an [emissions fork](../blend-whitepaper.md#emission-migration) - in which case the old version of the protocol still functions as expected; it just stops receiving emissions.

### What's a BLND token?

BLND is Blend's platform token. It is issued to protocol users and can be deposited in the backstop module to insure lending pools.

### Are there risks when using Blend?

Using any financial application involves risk. There are four main risks users should consider when using Blend.

**Smart Contract Risk**

Blend functions using smart contracts. If a bug is discovered and exploited, it could result in a loss of user funds. To mitigate this risk, the Blend Protocol has been audited by third parties.\
Audit Reports:

[audits-and-bug-bounties.md](../audits-and-bug-bounties.md "mention")

#### Oracle Risk

Blend's lending pools rely on oracles to price assets accurately. If a pool's oracle stopped functioning, the pool's users could suffer a loss of user funds.

#### Asset Risk

Lenders using Blend lending pools are exposed to asset risk. High volatility in assets could cause the pool to take on bad debt. Lenders could suffer asset loss if the amount of bad debt exceeds the value of assets [backstopping](backstopping.md) the pool.

**Stellar Protocol Ledger Risk**

As with all decentralized ledgers, Stellar has its unique set of risks. You can read more about the Stellar Consensus Protocol and its risks [here](https://developers.stellar.org/docs/glossary/scp/).
