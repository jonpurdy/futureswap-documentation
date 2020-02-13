---
name: Factory
route: /factory
---
# Factory
Our contract has two factories. One to deploy an exchange that calls the other to create a snapshot liquidity token for that exchange

# Exchange Factory

## Creating an Exchange

### createExchange()

#### parameters
*  address _stableToken -> the stable token of the given exchange
* address payable _assetToken -> the asset token of the exchange
* address _chainlinkAsset --> the chainlink refrence oracle associated with the Asset token(can be found on chainlink docs or manually deploy mock assetToken for greater control)
* address _chainlinkStable --> Stable token chainlink contract
* bool _isWeth -> if true will auto wrap and unwrap eth as it is sent to the contract (set true only for weth as asset)
* bool _isDSR -> if true will deposit most stable token (not collateral) in the contract into the dai savings rate. Only works for DAI!!!
* string _name --> name of the liquidity token for the exchange
* string _symbol --> symbol of the liquidity token for the exchange.

#### actions
* calls create liquidity token on liquidity token factory
* deploys new exchange with inputed parameters
* links the exchange to the token by adding it as minter and renouncing itself as minter
* pushes the exchange address into an array 
* asset token address maps to exchange
* stable token address maps to exchange
* maps liquidity token to exchange
* sets the exchange in the incentives contract (see incentives.md for more info)
* increases exchange count
* transfers the current owner to own the exchange contract
* emits ExchangeCreated

### modifiers
* only voter requires a vote (can be overriden by owner) see voting.md for more info

## upgradeFactory()

### parameters
* address new factory implementation

### actions 
* transfers factorship of incentives contract see incentives.md for more

### modifiers
* onlyVoter

## upgradeIncentve() 

### parameters 
* new incentives implementation

### actions 
* requires message comes from the current incentives 
* updates the incentives address stored in contract

## Liquidity Token Factory

### createLiquidityToken()
   * should not be called directly (harmless just useless)
#### parameters

* all come from the factory

#### actions 
* deploy liquidity token snapshot token
* adds factory as minter 
* returns address of deplpoyed token

### upgradeIncentve()

#### parameters 
* new incentives address 

#### actions
* requires call comes from current incentives
* updates the new incentives contract in contract
