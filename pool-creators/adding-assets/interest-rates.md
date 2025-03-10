# Interest Rates

Interest rate parameters are used to define how much borrowers should be charged for borrowing assets at different utilization rates.

### Utilization Rate

An asset's utilization rate is the percentage of the asset's deposits that are currently being borrowed. It can be calculated using the following formula:

$$UtilizationRate = \frac{TotalBorrowed}{TotalSupplied}$$

### Base Interest Rate

An asset's base interest rate is dynamic - it's based on a three-tier utilization curve which is calculated with the following parameters (the parameters are set by the pool creator and modifiable by the [pool admin](../pool-management.md#pool-admin)).

#### Target Utilization (U\_T)

The target percentage of an asset’s supply that should be borrowed. This should be set high for assets that are intended to be borrowed and low for assets that are intended to be used as collateral. This value is stored with 7 decimals.

#### Base Rate (R\_base)

The reserve's minimum interest rate. This value has 7 decimals.

#### Rate Slope 1 (R\_1)

The rate at which an asset's borrowing interest rate increases when it's below its target utilization. This value has 7 decimals.

#### Rate Slope 2 (R\_2)

The rate at which an asset's interest rate increases when it's above its target utilization. This value has 7 decimals.

#### Rate Slope 3 (R\_3)

The rate at which an asset's interest rate increases when it's above 95% utilization. An asset should never be above 95% utilization as it causes liquidity issues for lenders; thus, this slope should typically be set fairly high. This value has 7 decimals.

#### Base Interest Rate Equation

$$
IR(U)= \begin{cases} (R_{base}+\frac{U}{U_T}R_1) & \text{if } U\leq U_T\\ (R_{base}+R_1+\frac{U-U_T}{0.95-U_T}R_2) & \text{if } U_T\lt U\leq 0.95\\ (R_{base}+R_1+R_2)+\frac{U-0.95}{0.05}R_3 & \text{if } 0.95\lt U\\ \end{cases}
$$

Sample base interest rates:

<figure><img src="../../.gitbook/assets/interest rates (1).png" alt=""><figcaption></figcaption></figure>

* IR\_1 is a low-utilization asset with:
  * U\_T= 0.5 | R\_1 = 0.05 | R\_2 = 0.25 | R\_3 = 0.5
* IR\_2 is a high-utilization asset with:
  * U\_T = 0.85 | R\_1 = 0.05 | R\_2 = 0.15 | R\_3 = 0.5
* IR\_3 is a fixed-rate asset with:
  * U\_T = 0.01 | R\_1 = 0.05 | R\_2 = 0 | R\_3 = 0

Desmos link: [https://www.desmos.com/calculator/hzqgduyymj](https://www.desmos.com/calculator/hzqgduyymj)

### Reactive Interest

In addition to being dynamic, interest rates are reactive. When an asset is below its target utilization, its interest rate will gradually decrease. When it’s above, the interest rate will increase. Target utilization should be set high for assets that are intended to be borrowed, like USDC, and low for assets that are designed to be primarily used as collateral.

This serves to ensure that capital remains efficiently allocated between BLND lending pools.

#### Rate Modifier

The rate modifier value is the reactive value that modifies interest rates in response to variation from the target utilization rate. This value has 9 decimals.

The rate modifier equation is:

$$
\ Rate Modifier = Util Rate Error * Reactivity Constant + Rate Modifier
$$

#### UtilRateError

The utilization rate error is the accumulation of how far off an assets utilization rate was from its target

$$
Util Rate Error = \Delta seconds * (U_T-U)
$$

#### Reactivity Constant

A value that governs how quickly interest rates adjust based on assets' target utilization. This should be set based on how quickly users are expected to react to market inefficiencies. Additionally, it should also be set higher for assets with high-utilization targets to prevent them from experiencing excessive rate volatility and lower for low-utilization target assets.

For example, a reactivity constant of 0.0000200 will cause interest to double in approximately 2 months if the utilization rate is steadily 10% higher than it should be, making it a good choice for high utilization target assets.

$$518400_{seconds}*0.00002_{ReactivityConstant}*0.1_{UtilizationError}*100_{ErrorScalar}=1.0368$$

$$RateModifier = 1.0368 + 1=2.0368$$

The full interest rate equation with the reactivity constant is

$$
\ IR(U)= \begin{cases} RM*(R_{base}+\frac{U}{U_T}R_1) & \text{if } U\leq U_T\\ RM*(R_{base}+R_1+\frac{U-U_T}{0.95-U_T}R_2) & \text{if } U_T\lt U\leq 0.95\\ RM*(R_{base}+R_1+R_2)+\frac{U-0.95}{0.05}R_3 & \text{if } 0.95\lt U\\ \end{cases}
$$

You'll notice that the rate modifier only modifies the first two interest rates; this is because the last rate slope should be seen as an emergency slope with a very high value that assets shouldn't normally venture into. Allowing the rate modifier to affect it would make interest rates too volatile.
