---
id: Trading
title: Trading
---
# Trading REST API

*Source*: [https://github.com/KyberNetwork/apis](https://github.com/KyberNetwork/apis)

The Trading API's role is to allow a user to be able to programmatically interact with the KyberNetwork contract without in depth understanding of blockchain and smart contracts. The API currently only supports trades between the supported ERC20 token and ETH.
___

## INDEX

<AUTOGENERATED_TABLE_OF_CONTENTS>

## NETWORK URL

| Network    | URL                          |
|:----------:|:----------------------------:|
| Test       | https://dev-api.knstats.com  |
| Production | https://api.kyber.network    |

## REFERENCE

### Endpoints

### `/trading/getList`

(GET) Returns a list of all possible tokens available for trade.
___
**Response:**
| Parameter  | Type   | Description                                                                                  |
|:----------:|:------:|:--------------------------------------------------------------------------------------------:|
| `name`     | string | Name of the asset in its native chain.                                                       |
| `decimals` | int    | Decimals that will be used to round-off the srcQty or dstQty of the asset in other requests. |
| `address`  | string | The address of the asset in its native chain.                                                |
| `symbol`   | string | The symbol of the asset in its native chain.                                                 |
| `id`       | string | A unique ID used by Kyber Network to identify between different symbols.                     |

Example:

```json
> curl "https://dev-api.knstats.com/trading/getList"
{
  "error": false,
  "data": [
    {
      "name": "Ethereum",
      "decimals": 18,
      "address": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
      "symbol": "ETH",
      "id": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee"
    },
    {
      "name": "Kyber Network",
      "decimals": 18,
      "address": "0xdd974d5c2e2928dea5f71b9825b8b646686bd200",
      "symbol": "KNC",
      "id": "0xdd974d5c2e2928dea5f71b9825b8b646686bd200"
    },
    {
      "name": "OmiseGO",
      "decimals": 18,
      "address": "0xd26114cd6ee289accf82350c8d8487fedb8a0c07",
      "symbol": "OMG",
      "id": "0xd26114cd6ee289accf82350c8d8487fedb8a0c07"
    },
    {
      "name": "Status Network",
      "decimals": 18,
      "address": "0x744d70fdbe2ba4cf95131626614a1763df805b9e",
      "symbol": "SNT",
      "id": "0x744d70fdbe2ba4cf95131626614a1763df805b9e"
    },
    {
      "name": "AELF",
      "decimals": 18,
      "address": "0xbf2179859fc6d5bee9bf9158632dc51678a4100e",
      "symbol": "ELF",
      "id": "0xbf2179859fc6d5bee9bf9158632dc51678a4100e"
    }
  ]
}
```
<br />

### `/trading/getInfo`

(GET) Returns a list of token enabled statuses of an Ethereum wallet. It indicates if the wallet can sell a token or not. If not, how many transactions he has to do in order to enable it.

**Arguments:**
| Parameter      | Type    | Required | Description                                            |
|:--------------:|:-------:|:--------:|:------------------------------------------------------:|
| `user_address` | string  | Yes      | The ETH address to get information from. |
___
**Response:**
| Parameter      | Type   | Description                                                             |
|:--------------:|:------:|:-----------------------------------------------------------------------:|
| `id`           | string | A unique ID used by Kyber Network to identify between different symbols |
| `enabled`      | bool   | Whether the user address has approved Kyber Network to spend the asset on their behalf. Applicable only to ERC20 tokens. See ‘allowance’ on the ERC20 standard. |
| `txs_required` | int    | Number of transactions required until the ID is enabled for trading. When `enabled` is True, `txs_required` is 0. When `enabled` is False, majority of the time `tx_required` is 1. |

Example response:
```json
> curl "https://dev-api.knstats.com/trading/getInfo?user_address=0x8fA07F46353A2B17E92645592a94a0Fc1CEb783F"
{
  "error":false,
  "data":[
    {
      "id":"0xbf2179859fc6d5bee9bf9158632dc51678a4100e",
      "enabled":true,
      "txs_required":0
    },
    {  
      "id":"0x595832f8fc6bf59c85c527fec3740a1b7a361269",
      "enabled":false,
      "txs_required":1
    },
    {  
      "id":"0xd26114cd6ee289accf82350c8d8487fedb8a0c07",
      "enabled":true,
      "txs_required":0
    },
    {  
      "id":"0x0f5d2fb29fb7d3cfee444a200298f468908cc942",
      "enabled":true,
      "txs_required":0
    }
  ]
}
```
<br />

### ` /trading/enableCurrency`

(GET) Returns all needed information for a user to sign and do a transaction, and to enable a token to be able to sell as mentioned in #trading-getinfo.

**Arguments:**
| Parameter        | Type     | Required | Description                                             |
|:----------------:|:--------:|:--------:|:-------------------------------------------------------:|
| `user_address`   | string   | Yes      | The ETH address of the user that will enable the asset. |
| `id`             | string   | Yes      | The unique ID of the destination asset.                 |
| `gas_price`      | string   | Yes      | One of the following 3: `low`, `medium`, `high`. Priority will be set according to the level defined. |
___
**Response:**
| Parameter      | Type   | Description                                                                                           |
|:--------------:|:------:|:-----------------------------------------------------------------------------------------------------:|
| `from`           | string | The ETH address of the user. Must match the `user_address` request parameter.                       |
| `to`      | string | The contract address of the token you want to enable trading in Kyber Network. Always verify this for security reasons. |
| `data` | string | Transaction data. This data needs to be signed and broadcasted to the blockchain. After the transaction has been mined, you can check the status with `/info/getAccount`. |
| `value` | string | Should always be equal to 0 for this operation. Always verify that the value is 0 for security reasons.      |
| `gasPrice` | string | Calculated ETHGasStation price according to the user's request. If you need to specify a price value, change this wei hex value. |
| `nonce` | string | The nonce of the account. If multiple conversions are requested at the same time, each request will have the same nonce as the API will return the nonce of the account's last mined transaction. |
| `gasLimit` | string | The gas limit required for the transaction. This value should not be altered unless for specific reasons. |

Example:
```json
> curl "https://dev-api.knstats.com/trading/enableCurrency?user_address=0x8fA07F46353A2B17E92645592a94a0Fc1CEb783F&id=0xdd974D5C2e2928deA5F71b9825b8b646686BD200&gas_price=medium"
{
  "from": "0x8fA07F46353A2B17E92645592a94a0Fc1CEb783F",
  "to": "0xdd974D5C2e2928deA5F71b9825b8b646686BD200",
  "data": "0x095ea7b3000000000000000000000000818e6fecd516ecc3849daf6845e3ec868087b7558000000000000000000000000000000000000000000000000000000000000000",
  "value": "0x0",
  "gasPrice": "0x6",
  "nonce": "0x3de",
  "gasLimit": "0x186a0"
}
```
<br />

### `/trading/get_ethrate_buy`

(GET) Returns the latest BUY conversion rate in ETH. For example, if you want to know how much ETH do you need to buy 1 DAI, you can use this function.

**Arguments:**
| Parameter | Type   | Required | Description                                      |
|:---------:|:------:|:--------:|:------------------------------------------------:|
| `id`      | string | Yes      | The `id` of the asset you want to buy using ETH. |
| `qty`     | float  | Yes      | A floating point number which will be rounded off to the decimals of the asset specified. The quantity is the amount of units of the asset you want to buy. |
___
**Response:**
| Parameter | Type   | Description                                                                                    |
|:---------:|:-------:|:---------------------------------------------------------------------------------------------:|
| `src_id`  | string  | The `id` of the source asset. It should be ETH for this endpoint.                             |
| `dst_id`  | string  | The `id` of the destination asset of the pair you want to get rates for. `id` should match one of the request input parameters specified in `id`. |
| `src_qty` | float[] | Array of floating point numbers which will be rounded off to the decimals of the `id` of ETH. |
| `dst_qty` | float[] | Array of floating point numbers which will be rounded off to the decimals of the `id` of the destination asset. They should match the request input parameter specified in `qty`. |

Example:
```json
> curl "https://dev-api.knstats.com/trading/get_ethrate_buy?id=0xdd974D5C2e2928deA5F71b9825b8b646686BD200&qty=300&id=0xd26114cd6EE289AccF82350c8d8487fedB8A0C07&qty=10.1"
{
  "error": false,
  "data": [
    {
      "src_id": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
      "dst_id": "0xdd974D5C2e2928deA5F71b9825b8b646686BD200",
      "src_qty": [
        0.5671077504725898
      ],
      "dst_qty": [
        300
      ]
    },
    {
      "src_id": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
      "dst_id": "0xd26114cd6EE289AccF82350c8d8487fedB8A0C07",
      "src_qty": [
        0.18035714285714283
      ],
      "dst_qty": [
        10.1
      ]
    }
  ]
}
```
<br />

### `/trading/get_ethrate_sell`

(GET) Returns the latest SELL conversion rate in ETH. For example, if you want to know how much ETH you will get by SELLing 1 DAI, you can use this function.


**Arguments:**
| Parameter | Type   | Required | Description                                                |
|:---------:|:------:|:--------:|:----------------------------------------------------------:|
| `id`      | string | Yes      | The `id` of the assets you want to sell using ETH. |
| `qty`     | float  | Yes      | A floating point number which will be rounded off to the decimals of the asset specified. The quantity is the amount of units of the asset you want to sell. |
___
**Response:**
| Parameter | Type   | Description                                    |
|:---------:|:-------:|:----------------------:|
| `src_id`  | string  | The `id` of the source asset of the pair you want to get rates for. `id` should match the request input parameters specified in `id`. |
| `dst_id`  | string  | The `id` of the destination asset. It should be ETH for this endpoint. |
| `src_qty` | float[] | Array of floating point numbers which will be rounded off to the decimals of the `id` of the source asset. They should match the request input parameter specified in `qty`. |
| `dst_qty` | float[] | Array of floating point numbers which will be rounded off to the decimals of the `id` of ETH. |

Example:
```json
> curl "https://dev-api.knstats.com/trading/get_ethrate_sell?id=0xdd974D5C2e2928deA5F71b9825b8b646686BD200&qty=300&qty=150&id=0xd26114cd6EE289AccF82350c8d8487fedB8A0C07&qty=10.1&qty=20.2&qty=30"
{
  "error": false,
  "data": [
    {
      "src_id": "0xdd974D5C2e2928deA5F71b9825b8b646686BD200",
      "dst_id": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
      "src_qty": [
        300,
        150
      ],
      "dst_qty": [
        0.55963380759,
        0.279816903795
      ]
    },
    {
      "src_id": "0xd26114cd6EE289AccF82350c8d8487fedB8A0C07",
      "dst_id": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
      "src_qty": [
        10.1,
        20.2,
        30
      ],
      "dst_qty": [
        0.17552908314026,
        0.35105816628052,
        0.521373514278
      ]
    }
  ]
}
```
<br />

### `/trading/trade`

(GET) Trade or convert an asset pair, from token A to token B.

**Arguments:**
| Parameter      | Type   | Required | Description                                                                                           |
|:--------------:|:------:|:--------:|:-----------------------------------------------------------------------------------------------------:|
| `user_address` | string | Yes      | The ETH address that will be executing the swap.                                                      |
| `src_id`       | string | Yes      | The `id` of the source asset of the pair you want to trade.                                           |
| `dst_id`       | string | Yes      | The `id` of the destination asset of the pair you want to trade.                                      |
| `src_qty`      | float  | Yes      | A floating point number representing the source amount in the conversion which will be rounded off to the decimals of the `id` of the source asset. |
| `min_dst_qty`  | float  | Yes      | A floating point number representing the source amount in the conversion which will be rounded off to the decimals of the `id` of the destination asset. It is the minimum destination asset amount that is acceptable to the user. A guideline would be to set it at 3% less the destination quantity in `getPair`, which indicates a 3% slippage. |
| `gas_price`    | float  | Yes      | One of the following 3: `low`, `medium`, `high`. Priority will be set according to the level defined. |
___
**Response:**
| Parameter  | Type   | Description                                                                                               |
|:----------:|:------:|:---------------------------------------------------------------------------------------------------------:|
| `from`     | string | The ETH address that executed the swap. Must match the `user_address` request input parameter. |
| `to`       | string | The contract address of the KyberNetwork smart contract. Verify that it should always be the address resolved from `kybernetwork.eth` ENS. |
| `data`     | string | Transaction data. This data needs to be signed and broadcasted to the blockchain. After the transaction has been mined, you can check the status with `/info/getAccount`. |
| `value`    | string | This will be equal to 0 in hex (0x0) if users trade from token to token or token to ETH (when `src_id` is the source asset of the token, not ETH). This will not be equal to 0 in hex (0x0) if users trade from ETH to token (when `src_id` is the source asset of ETH, not a token) |
| `gasPrice` | string | Calculated ETHGasStation price according to the user's request. If you need to specify a price value, chance this wei hex value. |
| `nonce` | string | The nonce of the account. If multiple conversions are requested at the same time, each request will have the same nonce as the API will return the nonce of the account's last mined transaction. |
| `gasLimit` | string | The gas limit required for the transaction. This value should not be altered unless for specific reasons. |

Example:
```json
> curl "https://dev-api.knstats.com/trading/trade?user_address=0x8fa07f46353a2b17e92645592a94a0fc1ceb783f&src_id=0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee&dst_id=0xdd974D5C2e2928deA5F71b9825b8b646686BD200&src_qty=0.0012&min_dst_qty=0.6&gas_price=medium"
{
  "from": "0x8fa07f46353a2b17e92645592a94a0fc1ceb783f",
  "to": "0x818e6fecd516ecc3849daf6845e3ec868087b755",
  "data": "0xcb3c28c7000000000000000000000000eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee00000000000000000000000000000000000000000000000000044364c5bb0000000000000000000000000000dd974d5c2e2928dea5f71b9825b8b646686bd2000000000000000000000000008fa07f46353a2b17e92645592a94a0fc1ceb783f800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001b1ae4d6e2ef5000000000000000000000000000000000000000000000000000000000000000000000",
  "value": "0x44364c5bb0000",
  "gasPrice": "0x39eda2b80",
  "nonce": "0x3f1",
  "gasLimit": "0x43d81"
}

```
<br />

### `/trading/price`

(GET) Retrieve in-depth information about price and other information about assets.
___
**Response:**
| Parameter          | Type   | Description                                               |
|:------------------:|:------:|:---------------------------------------------------------:|
| `timestamp`        | int    | Server timestamp in UTC.                                  |
| `pair`             | string | Pair name consisting of the quote and base asset symbols. |
| `quote_symbol`     | string | Symbol of the quote asset of the pair.                    |
| `base_symbol`      | string | Symbol of the base asset of the pair.                     |
| `past_24h_high`    | float  | Highest ASK price for the last 24 hours of the pair.      |
| `past_24h_low`     | float  | Highest BID price for the last 24 hours of the pair.      |
| `usd_24h_volume`   | float  | Volume for the last 24 hours in USD.                      |
| `eth_24h_volume`   | float  | Volume for the last 24 hours in ETH.                      |
| `token_24h_volume` | float  | Volume for the last 24 hours in tokens.                   |
| `current_bid`      | float  | Current (considering some X minute delay) BID price.      |
| `current_ask`      | float  | Current (considering some X minute delay) ASK price.      |
| `last_traded`      | float  | Last traded price in the exchange.                        |

Example:
```json
> curl "https://dev-api.knstats.com/trading/prices"
{
  "error": false,
  "data": [
    {
      "timestamp": 1536806619250,
      "quote_symbol": "KNC",
      "base_symbol": "ETH",
      "past_24h_high": 0.001937984496124031,
      "past_24h_low": 0.001857617770187944,
      "usd_24h_volume": 5566.2079180166,
      "eth_24h_volume": 31.8094685833,
      "token_24h_volume": 16865.433010686364,
      "current_bid": 0.001867351485999998,
      "current_ask": 0.0018868074209224932,
      "last_traded": 0.0018868074209224932,
      "pair": "KNC_ETH"
    },
    {
      "timestamp": 1536806619251,
      "quote_symbol": "OMG",
      "base_symbol": "ETH",
      "past_24h_high": 0.018518518518518517,
      "past_24h_low": 0.017266283397471997,
      "usd_24h_volume": 13871.8906588085,
      "eth_24h_volume": 78.4248866967,
      "token_24h_volume": 4381.367829085394,
      "current_bid": 0.017379117142599983,
      "current_ask": 0.0175141743763495,
      "last_traded": 0.01777996566748282,
      "pair": "OMG_ETH"
    }
  ]
}
```
