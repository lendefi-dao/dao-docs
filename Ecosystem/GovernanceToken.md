# GovernanceToken
[Git Source](https://github.com/nebula-labs-xyz/lendefi-dao/blob/07f5cb7369219dbffd648091ffbddb6d70a0157c/contracts/ecosystem/GovernanceToken.sol)

## Overview
The GovernanceToken contract implements the core token functionality for the Lendefi DAO ecosystem, serving as both a governance instrument and a bridgeable asset. It leverages OpenZeppelin's upgradeable contract framework and incorporates multiple token standards and security features.

## Architecture Analysis

### Contract Structure
- **Extensive inheritance**: Combines seven OpenZeppelin upgradeable contracts to provide comprehensive functionality
- **UUPS upgradeability pattern**: Implements the Universal Upgradeable Proxy Standard for future upgrades
- **Role-based access control**: Uses AccessControl for permission management
- **Governance features**: Incorporates ERC20Votes for on-chain governance

### Token Economics
- **Fixed supply model**: 50M tokens total supply
- **Predetermined distribution**:
  - 56% allocated to treasury
  - 44% allocated to ecosystem
- **Bridge mechanism**: Supports cross-chain functionality with safety limits (20K tokens max per bridge transaction)
- **Two-phase initialization**: Separates contract setup from token generation event

## Technical Assessment

### Strengths

1. **Security features**:
   - Role-based permission system with specialized roles
   - Pausability for emergency situations
   - Bridge transaction size limits
   - One-time TGE initialization protection
   - Proper supply cap enforcement in bridge minting

2. **Governance capabilities**:
   - Full ERC20Votes implementation for governance participation
   - Permit functionality for gasless approvals
   - Checkpoints for vote delegation

3. **Upgrade safety**:
   - Version tracking via the version variable
   - Storage gap for future extension
   - Restricted upgrade authorization
   - Event emissions for upgrade transparency

4. **Cross-chain design**:
   - Bridge functionality with safety limits
   - Supply preservation across chains

### Potential Concerns

1. **Centralization risks**:
   - Admin role has significant control (DEFAULT_ADMIN_ROLE)
   - No time-locks or multi-signature requirements for sensitive operations

2. **Bridge functionality**:
   - No mechanism to pause only bridge operations in emergency
   - No clear recovery path for failed bridge transactions
   - Fixed bridge limit with no adjustment mechanism

3. **Initial distribution**:
   - No vesting for initial token distribution
   - All tokens are immediately liquid at TGE

## Code Quality Assessment

- **Documentation**: Good NatSpec documentation for most functions and parameters
- **Error handling**: Proper use of custom errors with descriptive messages
- **Event emissions**: Comprehensive event logging for important state changes
- **Variable naming**: Clear and descriptive naming conventions
- **Gas optimization**: Standard OpenZeppelin patterns with reasonable efficiency


**Inherits:**
ERC20Upgradeable, ERC20BurnableUpgradeable, ERC20PausableUpgradeable, AccessControlUpgradeable, ERC20PermitUpgradeable, ERC20VotesUpgradeable, UUPSUpgradeable

Burnable contract that votes and has BnM-Bridge functionality

*Implements a secure and upgradeable DAO governance token*

**Notes:**
- security-contact: security@nebula-labs.xyz

- oz-upgrades: 


## State Variables
### INITIAL_SUPPLY
Token supply and distribution constants


```solidity
uint256 private constant INITIAL_SUPPLY = 50_000_000 ether;
```


### MAX_BRIDGE_AMOUNT

```solidity
uint256 private constant MAX_BRIDGE_AMOUNT = 20_000 ether;
```


### TREASURY_SHARE

```solidity
uint256 private constant TREASURY_SHARE = 56;
```


### ECOSYSTEM_SHARE

```solidity
uint256 private constant ECOSYSTEM_SHARE = 44;
```


### PAUSER_ROLE
*AccessControl Pauser Role*


```solidity
bytes32 internal constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```


### BRIDGE_ROLE
*AccessControl Bridge Role*


```solidity
bytes32 internal constant BRIDGE_ROLE = keccak256("BRIDGE_ROLE");
```


### UPGRADER_ROLE
*AccessControl Upgrader Role*


```solidity
bytes32 internal constant UPGRADER_ROLE = keccak256("UPGRADER_ROLE");
```


### initialSupply
*Initial token supply*


```solidity
uint256 public initialSupply;
```


### maxBridge
*max bridge passthrough amount*


```solidity
uint256 public maxBridge;
```


### version
*number of UUPS upgrades*


```solidity
uint32 public version;
```


### tge
*tge initialized variable*


```solidity
uint32 public tge;
```


### __gap

```solidity
uint256[50] private __gap;
```


## Functions
### constructor

**Note:**
oz-upgrades-unsafe-allow: constructor


```solidity
constructor();
```

### initializeUUPS

Sets up the initial state of the contract, including roles and token supplies.

*Initializes the UUPS contract.*

**Notes:**
- requires: The guardian address must not be zero.

- events-emits: [Initialized](/contracts/ecosystem/GovernanceToken.sol/contract.GovernanceToken.md#initialized) event.

- throws: CustomError("ZERO_ADDRESS") if the guardian address is zero.


```solidity
function initializeUUPS(address guardian) external initializer;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`guardian`|`address`|The address of the guardian (admin).|


### initializeTGE

Sets up the initial token distribution between the ecosystem and treasury contracts.

*Initializes the Token Generation Event (TGE).*

**Notes:**
- requires: The ecosystem and treasury addresses must not be zero.

- requires: TGE must not be already initialized.

- events-emits: [TGE](/contracts/ecosystem/GovernanceToken.sol/contract.GovernanceToken.md#tge) event.

- throws: CustomError("ZERO_ADDRESS") if any of the addresses are zero.

- throws: CustomError("TGE_ALREADY_INITIALIZED") if TGE is already initialized.


```solidity
function initializeTGE(address ecosystem, address treasury) external onlyRole(DEFAULT_ADMIN_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`ecosystem`|`address`|The address of the ecosystem contract.|
|`treasury`|`address`|The address of the treasury contract.|


### pause

This function can be called by an account with the PAUSER_ROLE to pause the contract.

*Pauses all token transfers and operations.*

**Notes:**
- requires-role: PAUSER_ROLE

- events-emits: {Paused} event from PausableUpgradeable


```solidity
function pause() external onlyRole(PAUSER_ROLE);
```

### unpause

This function can be called by an account with the PAUSER_ROLE to unpause the contract.

*Unpauses all token transfers and operations.*

**Notes:**
- requires-role: PAUSER_ROLE

- events-emits: {Unpaused} event from PausableUpgradeable


```solidity
function unpause() external onlyRole(PAUSER_ROLE);
```

### bridgeMint

Mints new tokens to a specified address, called by an account with the BRIDGE_ROLE.

*Facilitates Bridge BnM functionality called by the bridge app.*

**Notes:**
- requires-role: BRIDGE_ROLE

- requires: Amount must not exceed maxBridge.

- requires: Total supply must not exceed initialSupply.

- events-emits: [BridgeMint](/contracts/ecosystem/GovernanceToken.sol/contract.GovernanceToken.md#bridgemint) event

- throws: CustomError("BRIDGE_LIMIT") if the amount exceeds maxBridge.

- throws: CustomError("BRIDGE_PROBLEM") if the total supply exceeds initialSupply.


```solidity
function bridgeMint(address to, uint256 amount) external onlyRole(BRIDGE_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`to`|`address`|The address that will receive the minted tokens.|
|`amount`|`uint256`|The amount of tokens to mint.|


### nonces


```solidity
function nonces(address owner) public view override(ERC20PermitUpgradeable, NoncesUpgradeable) returns (uint256);
```

### _update


```solidity
function _update(address from, address to, uint256 value)
    internal
    override(ERC20Upgradeable, ERC20PausableUpgradeable, ERC20VotesUpgradeable);
```

### _authorizeUpgrade


```solidity
function _authorizeUpgrade(address newImplementation) internal override onlyRole(UPGRADER_ROLE);
```

## Events
### Initialized
*Initialized Event.*


```solidity
event Initialized(address indexed src);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`src`|`address`|sender address|

### TGE
*event emitted at TGE*


```solidity
event TGE(uint256 amount);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`amount`|`uint256`|token amount|

### BridgeMint
*event emitted when bridge triggers a mint*


```solidity
event BridgeMint(address indexed src, address indexed to, uint256 amount);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`src`|`address`|sender|
|`to`|`address`|beneficiary address|
|`amount`|`uint256`|token amount|

### Upgrade
*event emitted on UUPS upgrades*


```solidity
event Upgrade(address indexed src, address indexed implementation);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`src`|`address`|sender address|
|`implementation`|`address`|new implementation address|

## Errors
### CustomError
*CustomError message*


```solidity
error CustomError(string msg);
```

**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`msg`|`string`|error description message|

