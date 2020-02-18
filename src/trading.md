---
name: Trading
route: /trading
---

# Trading

Trading has several functions for interacting with the exchange to open and close positions.

### openLong()

#### parameters

- uint256 \_maxLeverage -> the maximum amount of allowed leverage for the exchange
- bool \_isDSR -> a boolean of if the pool is denominated in the stable token DAI and thus will be accruing th DAI Savings Rate (DSR) on the liquidity, not the collateral.
- uint256 \_collateral -> the stable token of the given exchange
- uint256 \_leverage -> the desired leverage in wei = 10x leverage = 10 wei
- Storage.State storage state --> state stuct passed through
- Storage.Instances storage instances --> instance used to get prices
- Storage.TokenPools storage tokenPools -> the contracts recorded token pools state
- Storage.DynamicFunding storage dynamicFunding -> the state of the dynamic funding rate's storage

#### actions

- gets stable token price
- requries that there is enough asset token not being used by other trades for this trade to reserve it.
- requries that leverage is between desired levels.
- requries that the trade size is appropriate
- talcs the collateral plus the trade open fee
- transfers collateral plus the trade open fee in
- calcs the full trade value
- gets the asset token price
- calls newTrade()
- updates the state for the long pools. (this is a seperate function due to running into `stack too deep` errors)

### openShort()

#### parameters

- uint256 \_maxLeverage -> the maximum amount of allowed leverage for the exchange
- uint256 \_collateral -> the stable token of the given exchange
- uint256 \_leverage -> the desired leverage in wei = 10x leverage = 10 wei
- Storage.State storage state --> state stuct passed through
- Storage.TokenPools storage tokenPools -> the contracts recorded token pools state
- Storage.DynamicFunding storage dynamicFunding -> the state of the dynamic funding rate's storage
- Storage.Instances storage instances --> instance used to get prices

#### actions

- gets stable token price
- requries that there is enough stable token not being used by other trades for this trade to reserve it.
- requries that leverage is between desired levels.
- requries that the trade size is appropriate
- talcs the collateral plus the trade open fee
- transfers collateral plus the trade open fee in
- calcs the full trade value
- calcs how much stable token needs to be borrowed for the trade
- gets the asset token price
- calls newTrade()

### closeTrade()

#### parameters

- Storage.State storage state --> state stuct passed through
- Storage.TokenPools storage tokenPools -> the contracts recorded token pools state
- Storage.DynamicFunding storage dynamicFunding -> the state of the dynamic funding rate's storage
- uint256 \_tradeId -> the ID of the trade
- bool \_isSecured -> if the exchange is in Secured mode or not
- address \_referral -> the address if any that the trade has been reffered by
- Storage.Instances storage instances --> instance used to get prices

#### actions

- gets trade data
- requries that the sender is the trader or that the trade is in Secured mode (if it is in Secured mode it will only return the collateral to the trader, there is no profit or loss)
- requries that the trade has some amount of shares or else if trade.newShares <= 0 the trade is closed
- calculates the FST reward for the trader
- calcs the redemption amount. Redemption amount returns how much asset the trade still controls. (this number can go up or down from the initial trade size if charged or earned the Dynamic Funding Rate)
- sets trade.newShares to = so that the trade is marked as close
- if long then call closeLong()
- if short then call closeShort()
- emit logTrade() event

### closeLong()

#### parameters

- Storage.TokenPools storage tokenPools -> the contracts recorded token pools state
- Storage.Trade storage trade -> all trades mapping
- Storage.Instances storage instances --> instance used to get prices
- uint256 \_redemptionAmount -> the ID of the trade
- bool \_isSecured -> if the exchange is in Secured mode or not
- bool \_isDSR -> a boolean of if the pool is denominated in the stable token DAI and thus will be accruing th DAI Savings Rate (DSR) on the liquidity, not the collateral.
- uint256 \_frontRunningTime -> a constant for the exchange that is in seconds. It is the amount of seconds that a trade has to be placed before an oracle updates or else the trader is labeled as a front runner

#### actions

- calc the amount of asset the trader has made or lost
- calls closeLongLogic()
- call rewardTrader()

### closeLongLogic()

#### parameters

- Storage.TokenPools storage tokenPools -> the contracts recorded token pools state
- Storage.Trade storage trade -> all trades mapping
- uint256 \_redemptionAmount -> the ID of the trade
- int256 \_assetDfrReturn -> the profit or loss in asset of the trade

#### actions

- if the redemptionAmount is > the assetTokenBorrowed at the start of the trade
  - calc the remaining value for the long borrow
  - calc the remaining long asset borrowPool exluding the trade
  - calc the return value in dollars that the trade has earned from DFR
  - update longBorrowValue in tokenPools
- else
  - calc the average asset price for all assets currently borrowed
  - update longBorrowValue in tokenPools
- update asset token borrow pool quantity in tokenPools
- update stable token borrow pool quantity in tokenPools
