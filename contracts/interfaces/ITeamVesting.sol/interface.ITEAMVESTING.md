# ITEAMVESTING
[Git Source](https://github.com/nebula-labs-xyz/lendefi-dao/blob/3d33c9e00b25d5e60bf76d9b812b64e31361ab4a/contracts/interfaces/ITeamVesting.sol)

**Note:**
security-contact: security@nebula-labs.xyz


## Functions
### start

*Getter for the start timestamp.*


```solidity
function start() external returns (uint256);
```
**Returns**

|Name|Type|Description|
|----|----|-----------|
|`<none>`|`uint256`|start timestamp|


### duration

*Getter for the vesting duration.*


```solidity
function duration() external returns (uint256);
```
**Returns**

|Name|Type|Description|
|----|----|-----------|
|`<none>`|`uint256`|duration seconds|


### end

*Getter for the end timestamp.*


```solidity
function end() external returns (uint256);
```
**Returns**

|Name|Type|Description|
|----|----|-----------|
|`<none>`|`uint256`|end timestamp|


### released

*Getter for the amount of token already released*


```solidity
function released() external returns (uint256);
```
**Returns**

|Name|Type|Description|
|----|----|-----------|
|`<none>`|`uint256`|amount of tokens released so far|


### releasable

*Getter for the amount of releasable `token` ERC20 tokens.*


```solidity
function releasable() external returns (uint256);
```
**Returns**

|Name|Type|Description|
|----|----|-----------|
|`<none>`|`uint256`|amount of vested tokens|


### release

*Release the tokens that have already vested.
Emits a [ERC20Released](/contracts/interfaces/ITeamVesting.sol/interface.ITEAMVESTING.md#erc20released) event.*


```solidity
function release() external;
```

### cancelContract

*Release the tokens that have already vested.
Emits a [ERC20Released](/contracts/interfaces/ITeamVesting.sol/interface.ITEAMVESTING.md#erc20released) event.
Refund the remainder to the timelock*


```solidity
function cancelContract() external returns (uint256 remainder);
```

## Events
### Cancelled
*Cancelled Event*


```solidity
event Cancelled(uint256 amount);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`amount`|`uint256`|that was refunded to the treasury|

### ERC20Released
*ERC20Released Event*


```solidity
event ERC20Released(address indexed token, uint256 amount);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`token`|`address`|address|
|`amount`|`uint256`|released|

### VestingInitialized
*Contract initialization event*


```solidity
event VestingInitialized(
    address indexed token, address indexed beneficiary, address indexed timelock, uint64 startTimestamp, uint64 duration
);
```

## Errors
### Unauthorized
Error thrown when an unauthorized address attempts a restricted action

*Used to restrict functions that should only be callable by the contract creator*


```solidity
error Unauthorized();
```

### ZeroAddress
Error thrown when a zero address is provided where a valid address is required

*Used in validation of constructor parameters*


```solidity
error ZeroAddress();
```

