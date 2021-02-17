## Headers
| UMIP-#    |                                                                                                                                          |
|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| UMIP Title | Add WBTCUSD, USDWBTC as price identifiers              |
| Authors    | Logan (Logan@opendao.io)                               |
| Status     | Draft                                                                                                                                 |
| Created    | February 2, 2021                                                                                                                           |
| Discourse Link   | https://discourse.umaproject.org/t/adding-wbtc-usd-price-identifier-umip/124                                              |


## Summary (2-5 sentences)
The DVM should support price requests for the WBTC/USD and USD/WBTC price index. These price identifiers will serve to support future developments involving WBTC. 

## Motivation

The DVM currently does not support the WBTC/USD or USD/WBTC price index. Supporting the WBTCUSD price identifier would enable UMA partners (like OpenDAO or others) to provide access to the asset via the UMA architecture.

The addition of this price identifier would enable DeFi users to deploy WBTC as collateral for minting a stable coin, or for other purposes. 

At the time of writing, WBTC is trading at $33,767.47 with a 24-hour trading volume of $346,285,873. There is a circulating supply of 117,000 WBTC coins with a max supply of 117,000. Uniswap is currently the most active market trading it.
More information on the WBTC network can be found on the website: https://wbtc.network/

## Markets and Data Sources

1)Markets

- Binance
- Sushiswap
- Uniswap 

These three exchanges should be used to construct the price.They comprise a significant amount of WBTC trade volume and have available pricefeeds on Cryptowatch. 


2) Pairs

Binance: WBTC/BTC, Sushiswap WBTC/ETH, Uniswap: WBTC/ETH


3) Provide recommended endpoints to query for real-time prices from each market listed.

- Binance: WBTC/BTC

 https://api.binance.com/api/v3/ticker/price?symbol=WBTCBTC

- Sushiswap:WBTC/ETH

https://sushiswap.fi/token/0x2260fac5e5542a773aa44fbcfedf7c193bc2c599

- Uniswap:WBTC/ETH

https://info.uniswap.org/pair/0xbb2b8038a1640196fbe3e38816f3e67cba72d940


4) How often is the provided price updated?

 - The lower bound on the price update frequency is a minute.


5) Provide recommended endpoints to query for historical prices from each market listed.

- Binance: WBTC/BTC 

https://api.binance.com/api/v3/klines?symbol=WBTCBTC&interval=1d

- Sushiswap:WBTC/ETH 

https://sushiswap.fi/token/0x2260fac5e5542a773aa44fbcfedf7c193bc2c599

- Uniswap:WBTC/ETH

https://info.uniswap.org/pair/0xbb2b8038a1640196fbe3e38816f3e67cba72d940


6) Do these sources allow for querying up to 74 hours of historical data?
- Yes


7) How often is the provided price updated?
- The lower bound on the price update frequency is a minute.


8) Is an API key required to query these sources?
- No


9) Is there a cost associated with usage?
- No


10) If there is a free tier available, how many queries does it allow for?
- The lower bound on the number of queries allowed per hour is >> 1000.


11) What would be the cost of sending 15,000 queries?

Approximately $0


## Price Feed Implementation
Associated WBTC price feeds are available via Cryptowat.ch, Sushiswap, and Uniswap. Conversion from WBTC/ETH to WBTC/USD price needed for Binance and Sushiswap data. No other further feeds required. However, recommendation is to add a 1 minute TWAP specification for the Uniswap and Sushiswap prices. This will help prevent any attempted manipulation of the pool prices.

## Technical Specifications

- Price Identifier Name: WBTC/USD
- Base Currency: WBTC
- Quote Currency: USD
- Intended Collateral Currency: USDC
- Collateral Decimals: 6 
- Rounding: Nearest 6th decimal place (fifth decimal place digit >= 5 rounds up and < 5 rounds down)

- Price Identifier Name: USD/WBTC
- Base Currency: USD
- Quote Currency: WBTC
- Intended Collateral Currency: WBTC
- Collateral Decimals: 8
- Rounding: Nearest 8th decimal place (ninth decimal place digit >= 5 rounds up and < 5 rounds down)

1) Does the value of this collateral currency match the standalone value of the listed quote currency?:
- YES

2) Is your collateral currency already approved to be used by UMA financial contracts?:
- YES


## Rationale

The addition of WBTCUSD and USDWBTC will serve users at projects that are partnered with companies (such as OpenDAO) which are able to utilize the UMA architecture. More directly, this furthers adoption of the protocol by encouraging a convergence of capital from different projects and by potentially increasing TVL significantly.

Currently the most liquid exchange with USD or stablecoin markets for WBTC is BKEX (17.7% volume) while most overall trade involving WBTC is on Uniswap (~30% volume). BKEX will not be used for defining the price ID, as it is not available through Cryptowatch; however, due to the large volume and high liquidity of WBTC, it should be acceptable to use other markets, even if less voluminous. WBTC can currently be traded on a wide variety of exchanges, including what are widely considered to be several top tier platforms.

In the current setting, there will need to be a significant event that erodes confidence in WBTC and the token for it to be a security or PR concern.



## Implementation

Voters should query for the price of WBTC/USD USD/WBTC at the price request timestamp on Binance, Sushiswap and Uniswap. Recommended endpoints are provided in the markets and data sources section above.

When using the recommended endpoints, voters should:

1) Use the open price of the OHLC period that the timestamp falls in.

2) Take the median of these results.

3) The median from step 2 should be rounded to six decimals to determine the WBTC/USD price. Furthermore, Recommendation is to add a 1 minute TWAP specification for the Uniswap and Sushiswap prices. This will help prevent any attempted manipulation of the pool prices.

4) The value of USD/WBTC will follow the exact same process but undergo one additional step: it will be the result of dividing 1/EGLDUSD.

For both implementations, voters should determine whether the returned price differs from broad market consensus. This is meant to provide flexibility in any unforeseen circumstances as voters are responsible for defining broad market consensus.

This is only a reference implementation, how one queries the exchanges should be varied and determined by the voter to ensure that there is no central point of failure.


## Security considerations

WBTC is centralized in that the BTC collateral is custodied by BitGo and thus presents a central source of potential failure that should be considered; in light of this, it is important to note that BitGo is one of the most reputable centralized custodians in the world and has highly advanced coin security protocols in place. Furthermore, BitGo is covered by digital asset insurance worth up to $100 million in the worst case scenario.

Adding this new identifier by itself poses little security risk to the DVM or priceless financial contract users. However, anyone deploying a new priceless token contract referencing this identifier should take care to parameterize the contract appropriately to the reference asset’s volatility and liquidity characteristics to avoid the loss of funds for synthetic token holders. Additionally, the contract deployer should ensure that there is a network of liquidators and disputers ready to perform the services necessary to keep the contract solvent.

$UMA-holders should evaluate the ongoing cost and benefit of supporting price requests for this identifier and also contemplate de-registering this identifier if security holes are identified. As noted above, $UMA-holders should also consider re-defining this identifier as liquidity in the underlying asset changes, or if added robustness (eg via TWAPs) are necessary to prevent market manipulation.
