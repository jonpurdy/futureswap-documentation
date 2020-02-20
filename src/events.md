---
name: Events
route: /events
---

# Events

## Liquidity events

### UpdateLiquidity

```js
event UpdateLiquidity(
    address indexed _provider,
    uint256 _assetTokenAmount,
    uint256 _stableTokenAmount,
    uint256 _LSTPrice,
    uint256 _LiquidityMinted,
    bool _addedLiq,
    uint256 _timestamp
);
```

## Trading events

To reduce gas prices and provide realtime experiences, we use event data on the frontend to retrieve trade information.

### Working with open trades on the frontend

To get the full context of an open trade:

1. We need to query the `TradeClose` event to see if the trade has been closed.

2. We need to query the `AddCollateral` event to see if any collateral has been added.

3. For front running protection, we can't be certain about the open price of a trade until up to 3 minutes after a trade has been made. There's more context below, commented above `_oracleRoundId`

### TradeOpen

```js
// triggered when a new trade is opened
event TradeOpen(
    // Starts at 0 for an exchange and is incremented with each new trade open.
    // Trade closes, luqidations, and collateral updates will use this same ID.
    uint256 indexed _tradeId,
    // The address of the trader
    address indexed _tradeOwner,
    // true for long, false for short
    bool _isLong,
    // The amount of collateral in stable tokens put down
    uint256 _collateral,
    // The amount of leverage chosen
    uint256 _leverage,
    // The Asset price at open, not adjusted for potential front running protection
    uint256 _openPrice,
    // The fee for opening
    uint256 _openFee,
    // The chainlink oracle round, which is incremened with each new price oracle
    // update. We can use this on the client side to deterimine if an open trade has
    // been front run protected by pinging the oracle for the next price update time
    // and comparing that with the trade time and the exchanges frontRunningTime.
    uint256 _oracleRoundId,
    // The time at which the trade went through
    uint256 _timestamp,
    // 3rd party apps can pass in their address to earn incentives.
    address indexed _referral
);
```

Here's an example of how to derive the asset amount from `TradeOpen`:

```js
const assetAmount = toBn(_collateral)
    .mul(toBn(toWei(_leverage)))
    .div(toBn(_openPrice))
    .toString()
}
```

### TradeClose

For closed trades, we need to query `TradeLiquidate` to determine if the trade was closed or if it was liquidated.

We can get more context on the history of `TradeClose` by quering `AddCollateral` and `TradeOpen` events for the same ID.

```js
// triggered when a trade is closed
event TradeClose(
    uint256 indexed _tradeId, // same as in TradeOpen
    address indexed _tradeOwner,  // same as in TradeOpen
    bool _isLong,  // same as in TradeOpen
    // The collateral of the trade at close time, which would be different then
    // collateral at open if added when trade
    uint256 _collateral,
    // The open asset price, which takes into account the potential adjusted open
    // price for front run protection.
    uint256 _protectedOpenPrice,
    // The asset price at close
    uint256 _closePrice,
    // The Asset amount redeemed
    uint256 _redemptionAmount,
    // The time at which the trade went though.
    uint256 _timestamp,
    address indexed _referral
);
```

### AddCollateral

```js
// triggered when a user adds collateral to their trade
event AddCollateral(
    uint256 indexed _tradeId, // same as in TradeOpen
    // the quantity of added collateral
    uint256 _addedCollateral,
    address indexed _tradeOwner, // same as in TradeOpen
);
```

### TradeLiquidate

```js
// triggered when a trade a trade gets liquidated
event TradeLiquidate(
    uint256 indexed _tradeId, // same as in TradeOpen
    address indexed _tradeOwner, // same as in TradeOpen
    address indexed _liquidator, // the address the liquidator
    // The asset amount awarded to the liquidator
    uint256 _liquidatorReturn,
    // The asset amount returend to the trader
    uint256 _liqTraderReturn,
    // The time at which liquidation occured
    uint256 _timestamp,
    bool _isLong, // same as tradeOpened
    // The asset price at liqudation
    uint256 _liquidationPrice
);
```

### FrontRunning

```js
// Triggered when a trade is closed, if it was front run protected
event FrontRunning(
    uint256 indexed _tradeId, // same as in TradeOpen
    address indexed _tradeOwner, // same as in TradeOpen
    int256 _protectedOpenPrice // same as in tradeClose
);
```

## Incentives events

```js
event Payouts(
    address indexed _provider,
    uint256 _timestamp,
    uint256[] _weeks,
    bool indexed _isTrader
);
```

```js
event PayoutsLP(
    address indexed _provider,
    uint256 _timestamp,
    uint256[] _snapshotIds,
    address indexed _exchangeContract,
    address _liquidityToken
);
```
