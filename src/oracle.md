# Oracle 

* Our main oracle handling is located in Oracle.sol as a library. 

# getStableTokenPrice
* This is a read call to the chainlink oracle passed through when the exchange is created
* Values are multiplied by 10000000000 to keep the decimal places uniform to 18

# getAssetTokenPrice
* same as stable but for asset

# getOpenPrice
* to save space and gas execution on openTrade we do not save the price of the asset. Instead we save the round ID and call this funciton to return the correct price when called.
* this also takes in the chainlink address of when the trade was created as to get the correct historical data in case of a chainlink update

# getAssetPriceForClose
* This function is to deal with a frontrunning attack
* Chainlink updates it's oracle by sending out a hearbeat then waiting for 14 nodes to respond
* So we say the time from the heartbeat to the update plus gas cost is a frontrunning attack
* If a trade is made _frontRunningTime before the price update we assume it is a frontrunning attack and handle it 
* We do this by removing their profit or loss by giving them the price that they would have gotten on their next update. 
* ex: Alice opens long on ether at $100 30 seconds before the price update. The price updates to $101, on close Alice's open price becomes $101

# getLSTPrice
* This is the price of our liquidity token
* Since the token gets you an amount of stable and asset tokens then we calculate the price of our token by the value of tokens in our pools divided by the total supply. 

# padDecemials
* Helper function for when we need to convert non similar decimal tokens. We pad it up to 18 decimal places

# unpadDecimals 
* Only needed for stable to unpad the amount before we send transfer from our user in liquiudity 