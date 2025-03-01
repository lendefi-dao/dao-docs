# Ecosystem
[Git Source](https://github.com/nebula-labs-xyz/lendefi-dao/blob/05bc99b130950ac71cca48b96856e5ce17a94027/contracts/ecosystem/Ecosystem.sol)


## Overview
The Ecosystem contract serves as a token distribution and management hub for the Lendefi DAO, handling airdrops, rewards, token burning, and partnership vesting. It's designed with a strong emphasis on security, role-based access control, and upgradeability.

## Architecture and Design

### Contract Structure
- **Well-organized inheritance**: Follows a clear inheritance pattern using OpenZeppelin's upgradeable contracts
- **Role separation**: Uses granular role-based access control with specialized roles for each function
- **Token distribution mechanisms**: Implements four distinct distribution strategies (airdrops, rewards, burns, partnership vesting)
- **Supply tracking**: Maintains careful accounting of token allocations and distributions

### Token Economics
- **Defined allocations**: Allocates token supply in predetermined percentages:
  - 26% for rewards
  - 10% for airdrops
  - 8% for partnerships
- **Distribution limits**: Sets rational bounds with maxReward (0.1% of reward supply) and maxBurn (2% of reward supply)
- **Partnership vesting**: Creates dedicated vesting contracts using OpenZeppelin's VestingWallet

## Security Analysis

### Strengths
1. **Comprehensive role management**:
   - MANAGER_ROLE for administrative functions
   - PAUSER_ROLE for emergency controls
   - BURNER_ROLE for token burning
   - REWARDER_ROLE for issuing rewards
   - UPGRADER_ROLE for contract upgrades

2. **Multiple safety mechanisms**:
   - ReentrancyGuard on all fund-moving functions
   - Pausable functionality for emergency situations
   - Strict supply limits and accounting
   - SafeERC20 usage for token transfers
   - SafeCast for type conversions
   - Rejection of direct ETH transfers

3. **Input validation**:
   - Zero address checks
   - Amount validation with minimum and maximum thresholds
   - Supply limit enforcement
   - Gas-aware array processing (4000 recipient limit for airdrops)

### Potential Concerns
1. **Centralization risks**: Heavy reliance on role-based permissions could centralize control depending on role assignment
2. **Fixed allocation percentages**: Token allocations are hardcoded at initialization without adjustment mechanisms
3. **Immutable partner vesting**: Once created, partner vesting contracts cannot be modified
4. **No recovery mechanism**: No functionality to recover accidentally sent tokens

## Gas Efficiency
- Efficient loop construction in airdrop function
- Smart gas limit handling (limits airdrop arrays to 4000 entries)
- Use of custom errors instead of string messages
- Calldata parameters for arrays

## Upgradeability
- Properly implemented UUPS pattern
- Version tracking with increment on upgrade
- Storage gap for future extensions
- Clear upgrade authorization controls

## Code Quality and Documentation
- Excellent NatSpec documentation
- Clear error messages and custom errors
- Thorough event emissions for important state changes
- Detailed parameter validation explanations



**Inherits:**
[IECOSYSTEM](/contracts/interfaces/IEcosystem.sol/interface.IECOSYSTEM.md), Initializable, PausableUpgradeable, AccessControlUpgradeable, ReentrancyGuardUpgradeable, UUPSUpgradeable

Ecosystem contract handles airdrops, rewards, burning, and partnerships

*Implements a secure and upgradeable DAO ecosystem*

**Notes:**
- security-contact: security@nebula-labs.xyz

- copyright: Copyright (c) 2025 Nebula Holding Inc. All rights reserved.

- oz-upgrades: 


## State Variables
### BURNER_ROLE
*AccessControl Burner Role*


```solidity
bytes32 internal constant BURNER_ROLE = keccak256("BURNER_ROLE");
```


### PAUSER_ROLE
*AccessControl Pauser Role*


```solidity
bytes32 internal constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```


### UPGRADER_ROLE
*AccessControl Upgrader Role*


```solidity
bytes32 internal constant UPGRADER_ROLE = keccak256("UPGRADER_ROLE");
```


### REWARDER_ROLE
*AccessControl Rewarder Role*


```solidity
bytes32 internal constant REWARDER_ROLE = keccak256("REWARDER_ROLE");
```


### MANAGER_ROLE
*AccessControl Manager Role*


```solidity
bytes32 internal constant MANAGER_ROLE = keccak256("MANAGER_ROLE");
```


### tokenInstance
*governance token instance*


```solidity
ILENDEFI internal tokenInstance;
```


### rewardSupply
*starting reward supply*


```solidity
uint256 public rewardSupply;
```


### maxReward
*maximal one time reward amount*


```solidity
uint256 public maxReward;
```


### issuedReward
*issued reward*


```solidity
uint256 public issuedReward;
```


### maxBurn
*maximum one time burn amount*


```solidity
uint256 public maxBurn;
```


### airdropSupply
*starting airdrop supply*


```solidity
uint256 public airdropSupply;
```


### issuedAirDrop
*total amount airdropped so far*


```solidity
uint256 public issuedAirDrop;
```


### partnershipSupply
*starting partnership supply*


```solidity
uint256 public partnershipSupply;
```


### issuedPartnership
*partnership tokens issued so far*


```solidity
uint256 public issuedPartnership;
```


### version
*number of UUPS upgrades*


```solidity
uint32 public version;
```


### vestingContracts
*Addresses of vesting contracts issued to partners*


```solidity
mapping(address src => address vesting) public vestingContracts;
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

### receive

*Prevents receiving Ether*


```solidity
receive() external payable;
```

### initialize

Sets up the initial state of the contract, including roles and token supplies.

*Initializes the ecosystem contract.*

**Notes:**
- requires: All input addresses must not be zero.

- requires-role: DEFAULT_ADMIN_ROLE for the guardian.

- requires-role: PAUSER_ROLE for the pauser.

- events-emits: {Initialized} event.

- throws: CustomError("ZERO_ADDRESS_DETECTED") if any of the input addresses are zero.


```solidity
function initialize(address token, address guardian, address pauser) external initializer;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`token`|`address`|The address of the governance token.|
|`guardian`|`address`|The address of the guardian (admin).|
|`pauser`|`address`|The address of the pauser.|


### pause

Pauses all contract operations.

*Pause contract.*

**Notes:**
- requires-role: PAUSER_ROLE

- events-emits: {Paused} event from PausableUpgradeable


```solidity
function pause() external onlyRole(PAUSER_ROLE);
```

### unpause

Resumes all contract operations.

*Unpause contract.*

**Notes:**
- requires-role: PAUSER_ROLE

- events-emits: {Unpaused} event from PausableUpgradeable


```solidity
function unpause() external onlyRole(PAUSER_ROLE);
```

### airdrop

Distributes a specified amount of tokens to each address in the winners array.

*Performs an airdrop to a list of winners.*

**Notes:**
- requires-role: MANAGER_ROLE

- requires: Contract must not be paused

- requires: Amount must be at least 1 ether

- requires: Total airdropped amount must not exceed the airdrop supply

- requires: Number of winners must not exceed 4000 to avoid gas limit issues

- events-emits: {AirDrop} event

- throws: CustomError("INVALID_AMOUNT") if the amount is less than 1 ether

- throws: CustomError("AIRDROP_SUPPLY_LIMIT") if the total airdropped amount exceeds the airdrop supply

- throws: CustomError("GAS_LIMIT") if the number of winners exceeds 4000


```solidity
function airdrop(address[] calldata winners, uint256 amount)
    external
    nonReentrant
    whenNotPaused
    onlyRole(MANAGER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`winners`|`address[]`|An array of addresses to receive the airdrop.|
|`amount`|`uint256`|The amount of tokens to be airdropped to each address.|


### reward

Distributes a specified amount of tokens to a beneficiary address.

*Reward functionality for the Nebula Protocol.*

**Notes:**
- requires-role: REWARDER_ROLE

- requires: Contract must not be paused

- requires: Amount must be greater than 0

- requires: Amount must not exceed the maximum reward limit

- requires: Total rewarded amount must not exceed the reward supply

- events-emits: {Reward} event

- throws: CustomError("INVALID_AMOUNT") if the amount is 0

- throws: CustomError("REWARD_LIMIT") if the amount exceeds the maximum reward limit

- throws: CustomError("REWARD_SUPPLY_LIMIT") if the total rewarded amount exceeds the reward supply


```solidity
function reward(address to, uint256 amount) external nonReentrant whenNotPaused onlyRole(REWARDER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`to`|`address`|The address that will receive the reward.|
|`amount`|`uint256`|The amount of tokens to be rewarded.|


### burn

Burns a specified amount of tokens from the reward supply.

*Enables burn functionality for the DAO.*

**Notes:**
- requires-role: BURNER_ROLE

- requires: Contract must not be paused

- requires: Amount must be greater than 0

- requires: Amount must not exceed the maximum burn limit

- requires: Total burned amount must not exceed the reward supply

- events-emits: {Burn} event

- throws: CustomError("INVALID_AMOUNT") if the amount is 0

- throws: CustomError("BURN_SUPPLY_LIMIT") if the total burned amount exceeds the reward supply

- throws: CustomError("MAX_BURN_LIMIT") if the amount exceeds the maximum burn limit


```solidity
function burn(uint256 amount) external nonReentrant whenNotPaused onlyRole(BURNER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`amount`|`uint256`|The amount of tokens to be burned.|


### addPartner

Adds a new partner by creating a vesting contract and transferring the specified amount of tokens.

*Creates and funds a new vesting contract for a new partner.*

**Notes:**
- requires-role: MANAGER_ROLE

- requires: Contract must not be paused

- requires: Partner address must not be zero

- requires: Amount must be between 100 ether and half of the partnership supply

- requires: Total issued partnership tokens must not exceed the partnership supply

- events-emits: {AddPartner} event

- throws: CustomError("INVALID_ADDRESS") if the partner address is zero

- throws: CustomError("PARTNER_EXISTS") if the partner already has a vesting contract

- throws: CustomError("INVALID_AMOUNT") if the amount is not within the valid range

- throws: CustomError("AMOUNT_EXCEEDS_SUPPLY") if the total issued partnership tokens exceed the partnership supply


```solidity
function addPartner(address partner, uint256 amount, uint256 cliff, uint256 duration)
    external
    nonReentrant
    whenNotPaused
    onlyRole(MANAGER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`partner`|`address`|The address of the partner to receive the vesting contract.|
|`amount`|`uint256`|The amount of tokens to be vested.|
|`cliff`|`uint256`|The duration in seconds of the cliff period.|
|`duration`|`uint256`|The duration in seconds of the vesting period.|


### updateMaxReward

Allows updating the maximum reward that can be issued in a single transaction.

*Updates the maximum one-time reward amount.*

**Notes:**
- requires-role: MANAGER_ROLE

- requires: Contract must not be paused

- requires: New max reward must be greater than 0

- requires: New max reward must not exceed 5% of remaining reward supply

- events-emits: {MaxRewardUpdated} event

- throws: CustomError("INVALID_AMOUNT") if the amount is 0

- throws: CustomError("EXCESSIVE_MAX_REWARD") if the amount exceeds 5% of remaining reward supply


```solidity
function updateMaxReward(uint256 newMaxReward) external whenNotPaused onlyRole(MANAGER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`newMaxReward`|`uint256`|The new maximum reward amount.|


### _authorizeUpgrade

This function is called during the upgrade process to authorize the new implementation.

*Authorizes an upgrade to a new implementation.*

**Notes:**
- requires-role: UPGRADER_ROLE

- events-emits: {Upgrade} event


```solidity
function _authorizeUpgrade(address newImplementation) internal override onlyRole(UPGRADER_ROLE);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`newImplementation`|`address`|The address of the new implementation contract.|


