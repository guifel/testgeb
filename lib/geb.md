# Class: Geb

Main object of the library instantiating all useful GEB contracts and providing all helper functions needed.

## Hierarchy

-   **Geb**

## Index

### Constructors

-   [constructor](geb.md#constructor)

### Properties

-   [contracts](geb.md#contracts)

### Methods

-   [deployProxy](geb.md#deployproxy)
-   [getProxyAction](geb.md#getproxyaction)
-   [getSafe](geb.md#getsafe)
-   [multiCall](geb.md#multicall)

## Constructors

### constructor

\+ **new Geb**(`network`: GebDeployment, `provider`: GebProviderInterface | Provider): _[Geb](geb.md)_

_Defined in [packages/geb/src/geb.ts:45](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L45)_

**Parameters:**

| Name       | Type                                 | Description                                                                                     |
| ---------- | ------------------------------------ | ----------------------------------------------------------------------------------------------- |
| `network`  | GebDeployment                        | Either `'kovan'`, `'mainnet'` or an actual list of contract address from the deployment script. |
| `provider` | GebProviderInterface &#124; Provider | Either a Ethers.js provider or a Geb provider (Soon support for Web3 will be added)             |

**Returns:** _[Geb](geb.md)_

## Properties

### contracts

• **contracts**: _ContractApis_

_Defined in [packages/geb/src/geb.ts:43](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L43)_

Object containing all GEB contracts instances for low level interactions. All contracts object offer a one-to-one typed API to the underlying smart-contract.
Currently has the following contracts:

-   SafeEngine
-   AccountingEngine
-   TaxCollector
-   LiquidationEngine
-   OracleRelayer
-   GlobalSettlement
-   DebtAuctionHouse
-   PreSettlementSurplusAuctionHouse
-   PostSettlementSurplusAuctionHouse
-   SettlementSurplusAuctioneer
-   GebSafeManager
-   GetSafes
-   BasicCollateralJoin
-   CoinJoin
-   Coin (RAI ERC20 contract)
-   GebProxyRegistry
-   FixedDiscountCollateralAuctionHouse (For ETH-A)
-   Weth (ERC20)

## Methods

### deployProxy

▸ **deployProxy**(): _TransactionRequest_

_Defined in [packages/geb/src/geb.ts:88](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L88)_

Deploy a new proxy owned by the sender.

**Returns:** _TransactionRequest_

---

### getProxyAction

▸ **getProxyAction**(`ownerAddress`: string): _Promise‹[GebProxyActions](gebproxyactions.md)‹››_

_Defined in [packages/geb/src/geb.ts:76](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L76)_

Given an address returns a [[GebProxyAction]] object to execute bundled operations.
Important: This requires the address to have deployed a GEB proxy through the proxy registry contract. It will throw a `DOES_NOT_OWN_HAVE_PROXY` error if the address specified does not have a proxy. Use the [deployProxy](geb.md#deployproxy) function to get a new proxy.

**Parameters:**

| Name           | Type   | Description                                                            |
| -------------- | ------ | ---------------------------------------------------------------------- |
| `ownerAddress` | string | Externally owned user account, Ethereum address that owns a GEB proxy. |

**Returns:** _Promise‹[GebProxyActions](gebproxyactions.md)‹››_

---

### getSafe

▸ **getSafe**(`idOrHandler`: string | number): _Promise‹[Safe](safe.md)‹››_

_Defined in [packages/geb/src/geb.ts:96](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L96)_

Get the safe object

**Parameters:**

| Name          | Type                 | Description             |
| ------------- | -------------------- | ----------------------- |
| `idOrHandler` | string &#124; number | Safe Id or Safe handler |

**Returns:** _Promise‹[Safe](safe.md)‹››_

---

### multiCall

▸ **multiCall**‹**O1**, **O2**, **O3**›(`calls`: [MulticallRequest‹O1›, MulticallRequest‹O2›, MulticallRequest‹O3›]): _Promise‹[O1, O2, O3]›_

_Defined in [packages/geb/src/geb.ts:136](https://github.com/reflexer-labs/geb.js/blob/e238565/packages/geb/src/geb.ts#L136)_

Bundles several read only GEB contract call into 1 RPC single request. Useful for front-ends or apps that need to fetch many parameters from the contracts but want to minimize the network request and the load on the underlying Ethereum node.
The function takes as input an Array of GEB view contract calls.
IMPORTANT: You have to set the `multicall` parameter of the contract function to `true`, it is the always the last parameter of the function.
Multicall works for all contracts in the `Geb.contracts` and can be use with any contract that inherit the `BaseContractApi`. Note that it does not support non-view calls (Calls that require to pay gas and change the state of the blockchain).

Example:

```typescript
import { ethers } from 'ethers'
import { Geb } from 'geb.js'

const provider = new ethers.providers.JsonRpcProvider(
    'http://kovan.infura.io/...'
)
const geb = new Geb('kovan', provider)

const [globalDebt, collateralInfo] = await geb.multiCall([
    geb.contracts.safeEngine.globalDebt(true), // !! Note the last parameter set to true.
    geb.contracts.safeEngine.collateralTypes(ETH_A, true),
])

console.log(`Current global debt: ${globalDebt.toString()}`)
console.log(`Current ETH_A debt: ${collateralInfo.debtAmount}`)
```

**Type parameters:**

▪ **O1**

▪ **O2**

▪ **O3**

**Parameters:**

| Name    | Type                                                               | Description                                                                                                                                              |
| ------- | ------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `calls` | [MulticallRequest‹O1›, MulticallRequest‹O2›, MulticallRequest‹O3›] | Return value from a view GEB contract call. !! The GEB contract object needs to be call with the parameter `multicall` set to `true`, see example above. |

**Returns:** _Promise‹[O1, O2, O3]›_

Promise<T[]> Array with the result from their respective requests.
