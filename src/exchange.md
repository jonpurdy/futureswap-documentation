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


#### tip
* Formula to calculate the amount to send the max amount (stable or asset) to the exchange to fix the balance.
* Get the imbalance amount from the contract
* let imbalance = await fs.calculateImbalance()

* let x = (imbalance * 0.99) / 2;
* let y = imbalance - x * 2;
* let difference = x + y;
* let totalToSend = imbalance - difference;
* now use fs.getImbalance(true||false, totalToSend) to send the exact amount to fixed the imbalance
