---
name: Liqudity Token
route: /liquidityToken
---
# Liquidity Token
* The liquidity token gets created by the factory and deployed by the liquidity token factory (see factory.md) 
* It is essentially an open zeppelin snapshot token with minting and burning.
* The minter role is held only by the exchange
* The exchange can burn from without permission to avoid an extra transaction by user. This can only be called on remove liquidity. This is in ERC20.sol burnFrom function
* It also requires that the incentive contract is the only contract calling snapshot