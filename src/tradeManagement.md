---
name: tradeManagment
route: /tradeManagment
---

# Trade Management

## Open A Trade
To open a trade a user can call the ```openLong(uint256 _collateral, uint256 _leverage)``` to open a long or ```openShort(uint256 _collateral, uint256 _leverage)``` to open a short. Two parameters are passed through. 
1. How much collateral to deposit denominated in wei
2. What leverage the trader wants that is > 1 and <= 20

Both of these input parameters will create a position size. 
* position size = collateral * leverage 

The Collateral + initFee (see Init Fee section) is how much the user pays.

The Position size is how much the user's trade size is and how much the contrat "annexes" off. 

A Dynamic Funding rate will be applied. Read More about DFR here.

A new trade struct will be created 
```
Storage.Trade(
            stableTokenCollateral,
            openPrice,
            long,
            userAddress,
            assetTokenBorrowed,
            stableTokenBorrowed,
            newShares
        );
```
* stableTokenCollateral --> User collateral -> uint256
* openPrice -> Price of Asset class at open --> uint256
* Long -> Is it a long --> bool
* UserAddress --> msg.sender --> Address
* assetTokenBorrowed --> 0 if short, if long position size --> uint256
* stableTokenBorrowed --> 0 if long if short position size --> uint256
* newShares --> see DFR --> uint256

To read this at any time call ```tradeIdToTrade(uint256 _tradeId)```

Finally global variables are updated 
* For Longs
    * assetTokenBorrowPool -> amount of asset borrrowed (add our user's position size)
    * stableTokenCollateralPool --> All collateral put up by users (add in the user's collateral)
* For Shorts 
    * stableTokenBorrowPool -> amount of stable borrrowed (add our user's position size)
    * stableTokenCollateralPool --> All collateral put up by users (add in the user's collateral)
    * shortBorrowValue --> TODO
## Open Long Events

Logged Events 
* ```event LongOpened(address indexed provider, uint256 assetTokenAmount, uint256 tradeId, uint256 timestamp)```
    * Provider -> address that opened trade
    * assetTokenAmount -> The Position Size 
    * tradeId -> Id of trade made
    * timestamp -> When the trade was made




## Open Short Events
* ```event ShortOpened(address indexed provider, uint256 assetTokenAmount, uint256 tradeId, uint256 timestamp)```
    * Provider -> address that opened trade
    * assetTokenAmount -> The Position Size 
    * tradeId -> Id of trade made
    * timestamp -> When the trade was made

## Initation Fee 
* Each trade requires an initiation fee to be made. These fees act as payment to the liquidity providers. 
* The initFee is subject to change and is stored in 
* The actual fee paid is initFee * position size 
* There is a discount applied for having FSTokens and can be found by quering ```function getDiscountedInitFee() ```. It will return a uint
