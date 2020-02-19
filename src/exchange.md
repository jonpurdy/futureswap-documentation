---
name: Exchange
route: /exchange
---

# Exchange
* The exchange enables traders to balance the pools (asset & stable) of an exchange by trading for the needed token, which balances the difference. This concept is similar to an unbalanced pool in Uniswap where arbitragers are continually balancing the pools

### calculateImbalance()

#### parameters
* none

#### actions
* returns the current imbalance and a boolean, true is the asset false is the stable token

### getImbalance()

#### parameters
* bool _tradingAsset - boolean for trading the asset (true) or the stable (false)
* uint256 _amount- how much to transfer in
 

#### actions
* returns a uint256 which represents the amount you would receive for the trade


### internalExchange()

#### parameters
* uint256 _amount- how much to trade
* bool _tradingAsset - boolean for trading the asset (true) or the stable (false)
 

#### actions
* returns a uint256 which represents the amount you received for the trade
