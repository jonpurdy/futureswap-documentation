---
name: Deployment
route: /deployment
---

# Deploying the contracts

Deploying our whole suite of contracts can get a confusing as they all rely on each other and thus have to be deployed in certain order and after certain calls


# Partial Deploy



Partial deploying is the current setup of our repo. It requires a .env file. 

.env exmaple 
```
INFURA_KEY=
MNEMONIC=
```
If you go into /config/deployment you will see a file that looks like this 
```
const rinkeby = {
 Stable: "0x4Fdd8c53e92BDf52f1e66D7a15f1F4D7eD87d400",
 Asset: "0xcd8F273ad812494292a33279270B234f3E45155E",
 Chainlink: "0x1AddCFF77Ca0F032c7dCA322fd8bFE61Cae66A62",
 Incentive: "0x88e780413f0A3c79b3c3Ec8664Af31E3428edf18",
 ChainlinkStable: "0x03852E32dE75B93deEB11a2588E9c80e15C430b3",
 Minter: "0x77488ed6c9448E1A5dBe6772d6D31dB85108A53E",
 Upgrader: "0x1c094a3a03c1df449d2538ea0DCE8159b97E31d3",
 fsToken: "0xd66A73B18C38149f3D107b3Aa7E87F69316C06c4",
 Factory: "0x1B1C8a54f5e73aCc71A9aA8F6D24e12042d761aB"
}
```

The addresses relate to already deployed contracts and should be updated here if you update the contracts and want them to be connected. 

How to deploy 

## Voting

* coming soon


## FSToken

* First deploy the FS Token with no constructor arguments 
* deploy the proxy Admin
* Next deploy the proxy with
    * fsToken address
    * proxy Admin address
    * the bytecode of the initializer (see below for more details)
 
 bytecode initializer 
```
const input = web3.eth.abi.encodeFunctionCall({
      name: 'initialize',
      type: 'function',
      inputs: [{
        type: 'address',
        name: 'minter'
      }, {
        type: 'string',
        name: 'name'
      },
      {
        type: 'string',
        name: 'symbol'
      },
      {
        type: 'uint8',
        name: 'decimals'
      }]
    }, [addressOfSender, name, symbol, decimals]);    
```

* we will later have to renounce the ability for the deployer to mint but that has to be replaced with the incentives contract

## Incentive 

Next we have to deploy the incentive contract

* Deploy the incentive contract with the fsToken address
* Next call change amountmodifier() and setWeeklyAllowance() with a uint to represent both TODO JA: move these to constructor
* From the account you deployed the FSToken with call addMinter(FStokenAddress) and then renounce your mintership with renounceMinter()
* TODO: JA ONLY OWNER --> needs to be set to only voter
Only Factory will still need to be set, that will be done after the factory deployment


## Factory 

All Libraries need to be deployed and linked at this time

``` 
    await deployer.deploy(Oracle),
    await deployer.link(Oracle, ExchangeFactory),
    await deployer.deploy(Liquidity),
    await deployer.link(Liquidity, ExchangeFactory),
    await deployer.link(Liquidity, Trading),
    await deployer.deploy(DFR);
    await deployer.link(DFR, ExchangeFactory), await deployer.link(DFR, Trading);

    await deployer.deploy(Trading),
    await deployer.link(Trading, ExchangeFactory),
    await deployer.deploy(TradeManagement),
    await deployer.link(TradeManagement, ExchangeFactory),
    await deployer.deploy(InternalExchange)
    await deployer.link(InternalExchange, ExchangeFactory),

```

Next deploy the factory with the incentive contract address

* call ```transferFactoryship(factory.address)``` with the deployment account 
* call ```renounceFactorship()``` with the deployment account 
 