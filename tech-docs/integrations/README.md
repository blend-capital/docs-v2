# Integrations

This section contains information to help integrators like wallets, dApps, and other protocols utilize Blend.

Blend has two core integration points - the Backstop and the Lending Pool. The Backstop allows users to deposit backstop tokens (currently BLND-USDC LP tokens) into a pool's backstop to act as first loss capital, and earn a portion of the pools earned interest. The Lending Pool allows users to supply any of the pool's supported assets into the pool to earn interest, and optionally borrow against those funds.

Most integrations will interact with a Blend pool to supply tokens like USDC, EURC, or XLM to earn interest.

## Guides

* [Integrate with a Blend Pool](./integrate-pool.md)

## Resources

There are a few open souce resources that will assist with performing integrations.

### JS Blend SDK

[Github Link](https://github.com/blend-capital/blend-sdk-js)

The JS Blend SDK makes integrating with Blend significantly easier. It contains optimized loading functions for reading all Blend data from chain, and includes built-in math for calculating things like APYs and more.

It also serves as a resource and code example for various Blend actions that can be used as reference for any non-JS application.

### Blend UI

[Github Link](https://github.com/blend-capital/blend-ui)

The Blend UI is a React webapp built for interacting with the Blend protocol, and is the source code for both the Testnet and Mainnet Blend UI deployments.

### Blend Contract SDK

[Github Link](https://github.com/blend-capital/blend-contract-sdk)

The Blend Contract SDK is a Soroban based SDK for contracts that integrate Blend. It comes with all relevant contract clients and testutils to create test Blend deployments.



