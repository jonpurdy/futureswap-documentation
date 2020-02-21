---
name: Futureswap Main
route: /futureswapMain
---

# Futureswap.sol
* This is the main function. Very litte happens in this contract mainly there are shell functions that are calls to libraries in the context of the exchange

## Constructor 
* gets called by the exchange sets all our instances, initiates in secure mode

## Initialize contract
* sets all necessary values and removes the contract from secure mode. Anyone can call it 

Below are the libraries with the functions to call and the Events associated with them 

See Events.md for information on events 

### Trading
* see trading.md
    * openLong()
        * Event LogTrade
    * openShort()
        *  Event LogTrade
    * closeTrade()
        * Event closeTrade()
    * getDiscountedInitFee()

### Internal exchange
* see internalExchange.md
    * internalExchange()
    * getImbalance()
    * calculateImbalance()
    * calculateLiquidityReward()

### TradeManagment 
* see tradeManagment.md
    * addCollateral()
    * addLiquidity()
        * calls calculatesReward() from exhangeIncentives
    * removeLiquidity()
        * calls calculateReward from exchangeIncentives
    * liquidateTrade()

### DSR
* see DSR.md
    * addFundsToDSR()

### Oracle 
* see Oracle.md
    * getLSTPrice()

### Liquidity 
* see liquidity.md
    * stableTokenPool()
    * getAssetTokenAvailable()
    * getLiquidationPrice()

### Dynamic Function Rate 
* see dynamicFundingRate.md
    * getDFR()
    * getAdjustedTimeFee()

### Specific Trade details
* tradeIdToTrade(uint256 _tradeId)

### Votable Params 
* setImbalanceMultiplier()
* setIncentiveModifier()
* setDfrDivder()
* setTimeFee()
* setTokensToMaxFeeDivisor()
* setMaxFeeDivisor()
* setInitFee()
* setLiqTraderReturn()
* setLiquidatorReturn()
* setLiquidationRatio()
* setMaxLeverage()
* setFrontrunningTime()
* updateOracles()

### Ownable changes 
* initSecure() -> puts into secure mode, only allowed untill 6 months from launch. Anyone can close any trade at openPrice, no liquidity can be added or new trades can be made



