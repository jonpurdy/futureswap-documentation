---
name: Liquidation
route: /liquidation
---

# Liquidate Trade

As the underlying asset moves a given’s position can break the collateralization rule. Having positions with low amounts of collateral presents risks to the system. To dissipate this risk trades can be liquidated by anyone. Users that send liquidation transactions to close at risk positions receive a liquidator fee for their service which is a floating percentage of the remaining collateral in the trade. Currently the percentage that liquidators receive is 30% of the remaining collateral. This rate will very likely lower as a market equilibrium is established but starting higher represents a better margin of safety for the system as a whole. 5% of the remaining collateral get’s sent to liquidators as an additional fee for bearing risk. The remaining 65% of the collateral is returned to the trade initiator.
## Liquidation Fee Breakdown of Remaining Collateral
65% is returned to the trade initiator
30% is sent to the liquidator
5% is returned to the liquidity pool



