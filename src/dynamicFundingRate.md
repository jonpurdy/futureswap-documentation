---
name: Dynamic Funding Rate
route: /dynamicFundingRate
---

# Dynamic Funding Rate

## Concept

The dynamic funding rate (DFR) is a mechanism of the Futureswap system that aims to keep the long and short open volumes balanced. Futureswap relies on the volume of open longs and open shorts to be relatively balanced for the majority of the time. There will be deviations where the pools are very imbalanced due to large trades or spikes in volatility but this will impact the funding rate which in turn incentivizes more volume on the less popular side. In solidity charging a DFR to every single trade would be impractical so what Futureswap instead does is pools open long volumes and open short volume into two pools. Then since the DFR is supposed to be charged to all longs or shorts equally funds are moved from one of the pools to the next. When a trade is opened the underlying asset is deposited into the appropriate pool and the trader is issued their appropriate share ownership to that pool.

## Definition

The DFR is a % fee on the total trade size. The fee is based on an 8 hour period and is charged from either shorts and paid to longs or vice versa. There is a share of the DFR that is paid to liquidity providers as a form of interest for lending their assets to the trader.

## Example

If the DFR was at +0.01% that means that every 8 hours the longs would be paying the shorts 0.01%
If the DFR was at -0.23% that means that every 8 hours the shorts would be paying the longs 0.23%

## Calculations

To calculate the current DFR use the function called `getDFR` which can be found in the repo FS_CORE, ./contracts/lib/DFR.sol
getDFR takes in 3 parameters:
Storage.TokenPools
Storage.Instances
Storage.DynamicFunding

getDFR returns an int256 value. If the return value is positive it means that the long positions will be paying that return value percentage per 8 hours, and if the return value is negative the shorts will be paying that return value percentage per 8 hours.

## Time of Charge

The DFR is a percentage fee of the entire position size. Although it is based on an 8 hour period the fee is actually charged per the following contracts interaction:
openLong
openShort
closeTrade
For every interaction the contract calculates how many seconds passed since the last DFR was charged and calculates how much should be charged for the time used between the two and transitions value appropriately.

## getTradeShares

`getTradeShares` takes in 4 parameters:
Storage.TokenPools
Storage.DynamicFunding
\_isLong
\_tradeAsset

getTradeShares returns how many shares will be issued for the specific trade. Calculates the amount of share ownership the trade will own of the respective pool. Assets for longs and assets for shorts are held in two different pools and tracks the value of those pools. This is to allow for value being transfered between longs and shorts.

## redeemShares

`redeemShares` takes in 3 parameters:
Storage.TokenPools
Storage.DynamicFunding
Storage.Trade

redeemShares returns the value of the shares for the specific trade.
