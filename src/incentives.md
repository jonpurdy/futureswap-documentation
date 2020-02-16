---
name: Incentives
route: /incentives
---
# Incentives

* The incentives contract is meant to distribute users FST tokens based on 3 categories, trade usage, liquidity provided and referrals. The whole system is designed to pay out 27000000 tokens over approx a 5 year period. The rewards get paid out at the end of the week and payed based on your ratio of the


## newPayoutSplit()
* This function splits the balance of how we pay out our rewards between the three groups

### parameters 
* liquidityShare -> percent of weekly allowance recieved by liquidity provider
* traderShare -> percent of weekly allowance recieved by trader provider
* referralShare -> percent of weekly allowance recieved by referral provider

### actions
* requires all parameters passed in are between 0.01 and 1
* requires all shares = 1 ether in size
* splits weekley allowance based on parameters

### modifiers 
onlyVoter

## setExchange()
* This function can only be called by factory and allows exchanges to call into the countract to distribute rewards

### parameters 
* _exchange -> exchange to set

### actions
* set exchange
* increment exchange count

### modifiers 
* only factory can call this

## calculateReward() 
* This function is called by the exchange to calculate the reward of the three groups. 

### parameters 
* address _to -> the person to be rewarded
  address _referral -> the referrer to be rewarded
  address _liquidityToken -> the address of the liqudity token of that contract (liquidity payout only)
  uint256 _amount -> amount to be calculated for reward
  bool isTrade -> is this a trade event or liquidity event
  uint256 lastUpadtedweekleyTimeLiquidity ->
  uint256 _currentSnapshot -> current fsToken snapshot
  uint256 _exchangeMultiplier -> The multiplier from the exchange (exchanges can be weighted differently)
  uint256 _currentLiquidityPayoutMultiplier -> payout multiplier from liquidity

### Traders and Referrers reward
* rewards for traders
* we check first that the max amount of tokens has not been reached and the contract has not been made obsolete, if it has we skip everything and return false
* then we do time check which will be explained below
* if the call is a trade then we update the addresses reward by adding the amount to the week. Then we increment the amount that has been promised for traders that week
* then we check to see if the referral address is not 0 if it is we return if not we take the same steps for trade but this time to the referr's address and into the reffere's mapping and weeks total


### Liquidity Provider 
* this is handled in time payout and with the snapshot token

## timeCheck()
* Trader and Refferal side
    * time check is an internal function that handles the week and how we calculate how much to payout 
    * first we get the current time and subtract it from the weekley time
    * if that is less than one week nothing happens 
    * if that is greater than one week we take the weekley allowance trader and divide it by that week's minted (we also handle a divide by zero case) This gives us the trader payout multiplier for the week
    * We do the same for referral getting the referral payout multiplier for the week 
    * Finally it increases the week by 1, sets the weekleyTimeTrader to now and 0s out the amount minted from the reffere and trader that week 
* Liquidity rewards
    * does the same time math 
    * creates the instance for the liquidity token
    * If it has been a week it takes a snapshot, increments the current snapshot passed in and sets it to the global snapshot variable. Then it sets weekley time to now
    * If the snapshot is less then 1 nothing else happens
    * If it is more than 1 it calculates the payoue per exchange which is the weekley allowance divided by the total exchanges and adjusted for the exchange multiplier. Then it gets the multiplier by taking the payout per exchange and dividing by the total supply of the week before (this means liquidity providers must be bonded to liquidity for at least week to get a payout)
    * at the send of this call all global liquidity states are written into the specific exchange contract and stored there

    ## payoutTrader() 

    ### parameters 
    * address _target -> payout to
    * uint[] _week -> payout week array 

    ### actions
    * first checks to make sure we aren't over payout limit
    * loops through weeks making sure the target has a blanace that week and the week is currently over 
    * pays out the individualTraderPayout divided by the payout multiplier
    * thus if we promised more than the weekly allowance, the trader gets adjusted to less and if we promised less they get more
    * Zeros out there balance for the week 
    * increments the amount payed out 
    * mints them FST

    ## payoutReferrer()
    * similar to payout trader but for referrer

    ## payoutLiquidityProvider()

    ### parameters 
    * address _target -> target address
    * uint256[] memory _snapshotId -> snapshot ID to withdraw from 
    * address payable _exchangeContract -> the target exchange contract
    * address _liquidityToken -> the liquidity token associated with that exchange contract

    ### action
    * first checks to make sure we aren't over payout limit
    * loops through snapshot ID 
    * checks that the week is over, you haven't withdrawn and that you have a blance for the week 
    * get's the balance you had the week before thus binding you for a week and muliplies by the specific payout multiplier. 
    * adds to running total of payed out
    * marks the week as withdrawn
    * mints you FST

    ## upgradeIncentives()
    * This handles upgrading going forward and making the current contract obsolete. Exchanges will still work but will not get rewarded.

    ### parameters
    * address _newIncentives ->  new incentives contract
    * address _currentFactory -> current factory contract 
    * address _currentLiquidityTokenFactory -> current liquidity token factory

    ## actions
    * adds the new incentive as a minter and renounces itself (not backward compatiable)
    * sets obsolete to true
    * changes incentive address in both factories







