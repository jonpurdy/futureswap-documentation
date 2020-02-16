---
name: DAI Savings Rate
route: /dsr
---

# DAI Savings Rate 
* If enabled and the funds are in DAI this will deposit all the liquidity in the DSR 
* These are all internal functions unable to be called

## join()
* transfers funds from the user to the DSR

## joinTrade()
*  transfers funds from the contract to the DSR

## exit()
* transfers funds from the DSR to the user

## balance()
* returns the balance in the DSR (number should be slightly less since drip is not called)

## addFundsToDSR()
* In situations where there are an accumulation of funds in the contract and not in the DSR this will get called and the caller will be rewarded