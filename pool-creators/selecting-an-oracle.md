# Selecting an Oracle

Oracles are smart contracts that publish off-chain data on-chain. Blend pools use price oracles to get the prices of their assets.\
\
Pool creators must set a pool's oracle contract when they create a pool. This must be a single contract that can report prices for all assets in the pool.\

**Oracles CANNOT be changed after a pool is created, so please be very careful when selecting an oracle for a pool.**

Oracles must support the "lastprice" and "decimals" functions on the [SEP-40 oracle standard](https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0040.md).

### Test the Oracle

It is required to verify that "lastprice" works for all potential reserves the pool will maintain. Please note the Blend pool will always invoke the oracle with `Asset::Stellar({contract_address})` to fetch last price.

The oracle can be tested by using the `PoolOracle` object provided via the [Blend JS SDK](https://github.com/blend-capital/blend-sdk-js).

```js
import { PoolOracle } from '@blend-capital/blend-sdk';

// allow logging of bigints and maps
const replacer = (key, value) => {
  if (value instanceof Map) {
    return {
      dataType: "Map",
      value: Array.from(value.entries()),
    };
  } else if (typeof value == "bigint") {
    return {
      dataType: "BigInt",
      value: value.toString(),
    };
  } else {
    return value;
  }
};

const network = {
  passphrase: 'Public Global Stellar Network ; September 2015',
  rpc: 'some-rpc-url'
};

// the contract address of the oracle
const oracle_id = "C...";
// the contract addresses of the assets being included as reserves
const assets = ["C...", "C...", "C..."];

const oracle = await PoolOracle.load(network, oracle_id, assets);
// validate results
console.log(JSON.stringify(oracle, replacer, 2));
```

### Oracle Adapters

When a generalized oracle is insufficient for a pool (i.e. it doesn't support all assets the pool needs prices for), the pool creator may need to use an oracle adaptor. An oracle adaptor is a custom oracle contract that uses custom logic to aggregate multiple oracle feeds or impose desired behavior, such as reporting TWAP prices.

**Sample Oracle Adapter:**\
[https://github.com/blend-capital/oracle-aggregator](https://github.com/blend-capital/oracle-aggregator)

#### Types of Price Feeds

Pool creators may find different types of price feeds useful. The two main types used in most lending pools are:

* Spot: Spot price feeds report the current price of an asset.
* TWAPs (time-weighted-asset-prices): These price feeds report average asset price over a given time period. These price feeds are more difficult to manipulate than spot price feeds; they are preferable for high-volatility or low-liquidity assets. TWAPs come in two types:
  * GM-TWAPS: geometric mean TWAPs calculate the geometric mean of an asset's price over a given time period. They are more resistant to manipulation than AM-TWAPS.
  * AM-TWAPS: arithmetic mean TWAPs calculate the arithmetic mean of an asset's price over a given time period.&#x20;

#### Price Feed Aggregation

Ideally, price feeds should be aggregated across multiple sources to make them more manipulation-resistant. For example, a price feed reporting the XLM:USD spot price might aggregate the price by taking the average price from the Stellar DEX, Binance orderbook, and Coinbase orderbook.


#### Well Known Oracles

* [https://reflector.network/](https://reflector.network/)
