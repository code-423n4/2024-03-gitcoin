# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 12 |
| [GAS-2](#GAS-2) | Use assembly to check for `address(0)` | 6 |
| [GAS-3](#GAS-3) | Cache array length outside of loop | 2 |
| [GAS-4](#GAS-4) | State variables should be cached in stack variables rather than re-reading them from storage | 3 |
| [GAS-5](#GAS-5) | For Operations that will not overflow, you could use unchecked | 55 |
| [GAS-6](#GAS-6) | Functions guaranteed to revert when called by normal users can be marked `payable` | 3 |
| [GAS-7](#GAS-7) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 4 |
| [GAS-8](#GAS-8) | Using `private` rather than `public` for constants, saves gas | 3 |
| [GAS-9](#GAS-9) | Increments/decrements can be unchecked in for-loops | 4 |
| [GAS-10](#GAS-10) | Use != 0 instead of > 0 for unsigned integer comparison | 1 |
### <a name="GAS-1"></a>[GAS-1] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (12)*:
```solidity
File: contracts/IdentityStaking.sol

246:     selfStakes[msg.sender].amount += amount;

248:     userTotalStaked[msg.sender] += amount;

337:     communityStakes[msg.sender][stakee].amount += amount;

339:     userTotalStaked[msg.sender] += amount;

452:     totalSlashed[currentSlashRound] += sStake.slashedAmount;

459:     totalSlashed[currentSlashRound] += slashedAmount;

462:     sStake.slashedAmount += slashedAmount;

482:     totalSlashed[currentSlashRound] += comStake.slashedAmount;

489:     totalSlashed[currentSlashRound] += slashedAmount;

492:     comStake.slashedAmount += slashedAmount;

563:     selfStakes[staker].amount += amountToRelease;

574:     communityStakes[staker][stakee].amount += amountToRelease;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-2"></a>[GAS-2] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (6)*:
```solidity
File: contracts/IdentityStaking.sol

179:     if (tokenAddress == address(0)) {

318:     if (stakee == address(0)) {

354:     if (stakee == address(0)) {

385:     if (stakee == address(0)) {

545:     if (stakee == address(0)) {

549:     if (staker == address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-3"></a>[GAS-3] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (2)*:
```solidity
File: contracts/IdentityStaking.sol

189:     for (uint256 i = 0; i < initialSlashers.length; i++) {

193:     for (uint256 i = 0; i < initialReleasers.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-4"></a>[GAS-4] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (3)*:
```solidity
File: contracts/IdentityStaking.sol

467:       emit Slash(staker, slashedAmount, currentSlashRound);

491:       comStake.slashedInRound = currentSlashRound;

497:       emit Slash(staker, slashedAmount, currentSlashRound);

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-5"></a>[GAS-5] For Operations that will not overflow, you could use unchecked

*Instances (55)*:
```solidity
File: contracts/IdentityStaking.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

6: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

7: import {PausableUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/PausableUpgradeable.sol";

9: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

11: import {IIdentityStaking} from "./IIdentityStaking.sol";

189:     for (uint256 i = 0; i < initialSlashers.length; i++) {

193:     for (uint256 i = 0; i < initialReleasers.length; i++) {

234:     uint64 unlockTime = duration + uint64(block.timestamp);

238:     unlockTime < block.timestamp + 12 weeks ||

239:     unlockTime > block.timestamp + 104 weeks ||

246:     selfStakes[msg.sender].amount += amount;

248:     userTotalStaked[msg.sender] += amount;

266:     uint64 unlockTime = duration + uint64(block.timestamp);

270:     unlockTime < block.timestamp + 12 weeks ||

271:     unlockTime > block.timestamp + 104 weeks ||

296:     sStake.amount -= amount;

297:     userTotalStaked[msg.sender] -= amount;

325:     uint64 unlockTime = duration + uint64(block.timestamp);

329:     unlockTime < block.timestamp + 12 weeks ||

330:     unlockTime > block.timestamp + 104 weeks ||

337:     communityStakes[msg.sender][stakee].amount += amount;

339:     userTotalStaked[msg.sender] += amount;

364:     uint64 unlockTime = duration + uint64(block.timestamp);

368:     unlockTime < block.timestamp + 12 weeks ||

369:     unlockTime > block.timestamp + 104 weeks ||

403:     comStake.amount -= amount;

404:     userTotalStaked[msg.sender] -= amount;

441:     for (uint256 i = 0; i < numSelfStakers; i++) {

443:     uint88 slashedAmount = (percent * selfStakes[staker].amount) / 100;

448:     if (sStake.slashedInRound == currentSlashRound - 1) {

451:     totalSlashed[currentSlashRound - 1] -= sStake.slashedAmount;

452:     totalSlashed[currentSlashRound] += sStake.slashedAmount;

459:     totalSlashed[currentSlashRound] += slashedAmount;

462:     sStake.slashedAmount += slashedAmount;

463:     sStake.amount -= slashedAmount;

465:     userTotalStaked[staker] -= slashedAmount;

470:     for (uint256 i = 0; i < numCommunityStakers; i++) {

473:     uint88 slashedAmount = (percent * communityStakes[staker][stakee].amount) / 100;

478:     if (comStake.slashedInRound == currentSlashRound - 1) {

481:     totalSlashed[currentSlashRound - 1] -= comStake.slashedAmount;

482:     totalSlashed[currentSlashRound] += comStake.slashedAmount;

489:     totalSlashed[currentSlashRound] += slashedAmount;

492:     comStake.slashedAmount += slashedAmount;

493:     comStake.amount -= slashedAmount;

495:     userTotalStaked[staker] -= slashedAmount;

509:     if (block.timestamp - lastBurnTimestamp < burnRoundMinimumDuration) {

512:     uint16 roundToBurn = currentSlashRound - 1;

515:     ++currentSlashRound;

541:     if (slashRound < currentSlashRound - 1) {

562:     selfStakes[staker].slashedAmount -= amountToRelease;

563:     selfStakes[staker].amount += amountToRelease;

573:     communityStakes[staker][stakee].slashedAmount -= amountToRelease;

574:     communityStakes[staker][stakee].amount += amountToRelease;

577:     totalSlashed[slashRound] -= amountToRelease;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-6"></a>[GAS-6] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (3)*:
```solidity
File: contracts/IdentityStaking.sol

206:   function pause() external onlyRole(PAUSER_ROLE) whenNotPaused {

211:   function unpause() external onlyRole(PAUSER_ROLE) whenPaused {

218:   function _authorizeUpgrade(address) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-7"></a>[GAS-7] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

*Saves 5 gas per instance*

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

189:     for (uint256 i = 0; i < initialSlashers.length; i++) {

193:     for (uint256 i = 0; i < initialReleasers.length; i++) {

441:     for (uint256 i = 0; i < numSelfStakers; i++) {

470:     for (uint256 i = 0; i < numCommunityStakers; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-8"></a>[GAS-8] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (3)*:
```solidity
File: contracts/IdentityStaking.sol

65:   bytes32 public constant SLASHER_ROLE = keccak256("SLASHER_ROLE");

68:   bytes32 public constant RELEASER_ROLE = keccak256("RELEASER_ROLE");

71:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-9"></a>[GAS-9] Increments/decrements can be unchecked in for-loops
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

The change would be:

```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

These save around **25 gas saved** per instance.

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256`.

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

189:     for (uint256 i = 0; i < initialSlashers.length; i++) {

193:     for (uint256 i = 0; i < initialReleasers.length; i++) {

441:     for (uint256 i = 0; i < numSelfStakers; i++) {

470:     for (uint256 i = 0; i < numCommunityStakers; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="GAS-10"></a>[GAS-10] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

518:     if (amountToBurn > 0) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Missing checks for `address(0)` when assigning values to address state variables | 1 |
| [NC-2](#NC-2) | `constant`s should be defined rather than using magic numbers | 12 |
| [NC-3](#NC-3) | Control structures do not follow the Solidity Style Guide | 4 |
| [NC-4](#NC-4) | Function ordering does not follow the Solidity style guide | 1 |
| [NC-5](#NC-5) | Functions should not be longer than 50 lines | 10 |
| [NC-6](#NC-6) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 6 |
| [NC-7](#NC-7) | Consider using named mappings | 4 |
| [NC-8](#NC-8) | Take advantage of Custom Error's return value property | 33 |
| [NC-9](#NC-9) | Contract does not follow the Solidity style guide's suggested layout ordering | 1 |
| [NC-10](#NC-10) | Event is missing `indexed` fields | 6 |
| [NC-11](#NC-11) | `public` functions not called by the contract should be declared `external` instead | 1 |
| [NC-12](#NC-12) | Variables need not be initialized to zero | 4 |
### <a name="NC-1"></a>[NC-1] Missing checks for `address(0)` when assigning values to address state variables

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

198:     burnAddress = _burnAddress;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-2"></a>[NC-2] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (12)*:
```solidity
File: contracts/IdentityStaking.sol

201:     burnRoundMinimumDuration = 90 days;

238:       unlockTime < block.timestamp + 12 weeks ||

239:       unlockTime > block.timestamp + 104 weeks ||

270:       unlockTime < block.timestamp + 12 weeks ||

271:       unlockTime > block.timestamp + 104 weeks ||

329:       unlockTime < block.timestamp + 12 weeks ||

330:       unlockTime > block.timestamp + 104 weeks ||

368:       unlockTime < block.timestamp + 12 weeks ||

369:       unlockTime > block.timestamp + 104 weeks ||

430:     if (percent > 100 || percent == 0) {

443:       uint88 slashedAmount = (percent * selfStakes[staker].amount) / 100;

473:       uint88 slashedAmount = (percent * communityStakes[staker][stakee].amount) / 100;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-3"></a>[NC-3] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

236:     if (

268:     if (

327:     if (

366:     if (

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-4"></a>[NC-4] Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

1: 
   Current order:
   public initialize
   external pause
   external unpause
   internal _authorizeUpgrade
   external selfStake
   external extendSelfStake
   external withdrawSelfStake
   external communityStake
   external extendCommunityStake
   external withdrawCommunityStake
   external slash
   external lockAndBurn
   external release
   
   Suggested order:
   external pause
   external unpause
   external selfStake
   external extendSelfStake
   external withdrawSelfStake
   external communityStake
   external extendCommunityStake
   external withdrawCommunityStake
   external slash
   external lockAndBurn
   external release
   public initialize
   internal _authorizeUpgrade

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-5"></a>[NC-5] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (10)*:
```solidity
File: contracts/IIdentityStaking.sol

44:   function userTotalStaked(address staker) external view returns (uint88);

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IIdentityStaking.sol)

```solidity
File: contracts/IdentityStaking.sol

206:   function pause() external onlyRole(PAUSER_ROLE) whenNotPaused {

211:   function unpause() external onlyRole(PAUSER_ROLE) whenPaused {

218:   function _authorizeUpgrade(address) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}

229:   function selfStake(uint88 amount, uint64 duration) external whenNotPaused {

261:   function extendSelfStake(uint64 duration) external whenNotPaused {

285:   function withdrawSelfStake(uint88 amount) external whenNotPaused {

314:   function communityStake(address stakee, uint88 amount, uint64 duration) external whenNotPaused {

353:   function extendCommunityStake(address stakee, uint64 duration) external whenNotPaused {

384:   function withdrawCommunityStake(address stakee, uint88 amount) external whenNotPaused {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-6"></a>[NC-6] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (6)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

262:     if (selfStakes[msg.sender].amount == 0) {

301:     if (!token.transfer(msg.sender, amount)) {

315:     if (stakee == msg.sender) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

408:     if (!token.transfer(msg.sender, amount)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-7"></a>[NC-7] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

88:   mapping(address => uint88) public userTotalStaked;

91:   mapping(address => Stake) public selfStakes;

94:   mapping(address => mapping(address => Stake)) public communityStakes;

117:   mapping(uint16 => uint88) public totalSlashed;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-8"></a>[NC-8] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (33)*:
```solidity
File: contracts/IdentityStaking.sol

180:       revert AddressCannotBeZero();

231:       revert AmountMustBeGreaterThanZero();

243:       revert InvalidLockTime();

253:       revert FailedTransfer();

263:       revert AmountMustBeGreaterThanZero();

275:       revert InvalidLockTime();

289:       revert StakeIsLocked();

293:       revert AmountTooHigh();

302:       revert FailedTransfer();

316:       revert CannotStakeOnSelf();

319:       revert AddressCannotBeZero();

322:       revert AmountMustBeGreaterThanZero();

334:       revert InvalidLockTime();

344:       revert FailedTransfer();

355:       revert AddressCannotBeZero();

361:       revert AmountMustBeGreaterThanZero();

373:       revert InvalidLockTime();

386:       revert AddressCannotBeZero();

390:       revert AmountMustBeGreaterThanZero();

396:       revert StakeIsLocked();

400:       revert AmountTooHigh();

409:       revert FailedTransfer();

431:       revert InvalidSlashPercent();

438:       revert StakerStakeeMismatch();

510:       revert MinimumBurnRoundDurationNotMet();

520:         revert FailedTransfer();

542:       revert RoundAlreadyBurned();

546:       revert AddressCannotBeZero();

550:       revert AddressCannotBeZero();

555:         revert FundsNotAvailableToRelease();

559:         revert FundsNotAvailableToReleaseFromRound();

566:         revert FundsNotAvailableToRelease();

570:         revert FundsNotAvailableToReleaseFromRound();

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-9"></a>[NC-9] Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be:

1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

However, the contract(s) below do not follow this ordering

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

1: 
   Current order:
   ErrorDefinition.AddressCannotBeZero
   ErrorDefinition.AmountMustBeGreaterThanZero
   ErrorDefinition.CannotStakeOnSelf
   ErrorDefinition.FailedTransfer
   ErrorDefinition.InvalidLockTime
   ErrorDefinition.StakeIsLocked
   ErrorDefinition.AmountTooHigh
   ErrorDefinition.InvalidSlashPercent
   ErrorDefinition.StakerStakeeMismatch
   ErrorDefinition.FundsNotAvailableToRelease
   ErrorDefinition.FundsNotAvailableToReleaseFromRound
   ErrorDefinition.RoundAlreadyBurned
   ErrorDefinition.MinimumBurnRoundDurationNotMet
   VariableDeclaration.SLASHER_ROLE
   VariableDeclaration.RELEASER_ROLE
   VariableDeclaration.PAUSER_ROLE
   StructDefinition.Stake
   VariableDeclaration.userTotalStaked
   VariableDeclaration.selfStakes
   VariableDeclaration.communityStakes
   VariableDeclaration.currentSlashRound
   VariableDeclaration.burnRoundMinimumDuration
   VariableDeclaration.lastBurnTimestamp
   VariableDeclaration.burnAddress
   VariableDeclaration.totalSlashed
   VariableDeclaration.token
   EventDefinition.SelfStake
   EventDefinition.CommunityStake
   EventDefinition.SelfStakeWithdrawn
   EventDefinition.CommunityStakeWithdrawn
   EventDefinition.Slash
   EventDefinition.Burn
   FunctionDefinition.initialize
   FunctionDefinition.pause
   FunctionDefinition.unpause
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition.selfStake
   FunctionDefinition.extendSelfStake
   FunctionDefinition.withdrawSelfStake
   FunctionDefinition.communityStake
   FunctionDefinition.extendCommunityStake
   FunctionDefinition.withdrawCommunityStake
   FunctionDefinition.slash
   FunctionDefinition.lockAndBurn
   FunctionDefinition.release
   
   Suggested order:
   VariableDeclaration.SLASHER_ROLE
   VariableDeclaration.RELEASER_ROLE
   VariableDeclaration.PAUSER_ROLE
   VariableDeclaration.userTotalStaked
   VariableDeclaration.selfStakes
   VariableDeclaration.communityStakes
   VariableDeclaration.currentSlashRound
   VariableDeclaration.burnRoundMinimumDuration
   VariableDeclaration.lastBurnTimestamp
   VariableDeclaration.burnAddress
   VariableDeclaration.totalSlashed
   VariableDeclaration.token
   StructDefinition.Stake
   ErrorDefinition.AddressCannotBeZero
   ErrorDefinition.AmountMustBeGreaterThanZero
   ErrorDefinition.CannotStakeOnSelf
   ErrorDefinition.FailedTransfer
   ErrorDefinition.InvalidLockTime
   ErrorDefinition.StakeIsLocked
   ErrorDefinition.AmountTooHigh
   ErrorDefinition.InvalidSlashPercent
   ErrorDefinition.StakerStakeeMismatch
   ErrorDefinition.FundsNotAvailableToRelease
   ErrorDefinition.FundsNotAvailableToReleaseFromRound
   ErrorDefinition.RoundAlreadyBurned
   ErrorDefinition.MinimumBurnRoundDurationNotMet
   EventDefinition.SelfStake
   EventDefinition.CommunityStake
   EventDefinition.SelfStakeWithdrawn
   EventDefinition.CommunityStakeWithdrawn
   EventDefinition.Slash
   EventDefinition.Burn
   FunctionDefinition.initialize
   FunctionDefinition.pause
   FunctionDefinition.unpause
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition.selfStake
   FunctionDefinition.extendSelfStake
   FunctionDefinition.withdrawSelfStake
   FunctionDefinition.communityStake
   FunctionDefinition.extendCommunityStake
   FunctionDefinition.withdrawCommunityStake
   FunctionDefinition.slash
   FunctionDefinition.lockAndBurn
   FunctionDefinition.release

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-10"></a>[NC-10] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (6)*:
```solidity
File: contracts/IdentityStaking.sol

127:   event SelfStake(address indexed staker, uint88 amount, uint64 unlockTime);

135:   event CommunityStake(

145:   event SelfStakeWithdrawn(address indexed staker, uint88 amount);

151:   event CommunityStakeWithdrawn(address indexed staker, address indexed stakee, uint88 amount);

157:   event Slash(address indexed staker, uint88 amount, uint16 round);

162:   event Burn(uint16 indexed round, uint88 amount);

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-11"></a>[NC-11] `public` functions not called by the contract should be declared `external` instead

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

172:   function initialize(

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="NC-12"></a>[NC-12] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

189:     for (uint256 i = 0; i < initialSlashers.length; i++) {

193:     for (uint256 i = 0; i < initialReleasers.length; i++) {

441:     for (uint256 i = 0; i < numSelfStakers; i++) {

470:     for (uint256 i = 0; i < numCommunityStakers; i++) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Some tokens may revert when zero value transfers are made | 5 |
| [L-2](#L-2) | Missing checks for `address(0)` when assigning values to address state variables | 1 |
| [L-3](#L-3) | Initializers could be front-run | 4 |
| [L-4](#L-4) | Signature use at deadlines should be allowed | 10 |
| [L-5](#L-5) | Prevent accidentally burning tokens | 1 |
| [L-6](#L-6) | Solidity version 0.8.20+ may not work on other chains due to `PUSH0` | 1 |
| [L-7](#L-7) | Unsafe ERC20 operation(s) | 5 |
| [L-8](#L-8) | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 7 |
| [L-9](#L-9) | Upgradeable contract not initialized | 11 |
### <a name="L-1"></a>[L-1] Some tokens may revert when zero value transfers are made
Example: https://github.com/d-xo/weird-erc20#revert-on-zero-value-transfers.

In spite of the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted, some tokens, such as LEND will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

*Instances (5)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

301:     if (!token.transfer(msg.sender, amount)) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

408:     if (!token.transfer(msg.sender, amount)) {

519:       if (!token.transfer(burnAddress, amountToBurn)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-2"></a>[L-2] Missing checks for `address(0)` when assigning values to address state variables

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

198:     burnAddress = _burnAddress;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-3"></a>[L-3] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (4)*:
```solidity
File: contracts/IdentityStaking.sol

172:   function initialize(

178:   ) public initializer {

183:     __AccessControl_init();

184:     __Pausable_init();

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-4"></a>[L-4] Signature use at deadlines should be allowed
According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.

*Instances (10)*:
```solidity
File: contracts/IdentityStaking.sol

238:       unlockTime < block.timestamp + 12 weeks ||

239:       unlockTime > block.timestamp + 104 weeks ||

270:       unlockTime < block.timestamp + 12 weeks ||

271:       unlockTime > block.timestamp + 104 weeks ||

288:     if (sStake.unlockTime > block.timestamp) {

329:       unlockTime < block.timestamp + 12 weeks ||

330:       unlockTime > block.timestamp + 104 weeks ||

368:       unlockTime < block.timestamp + 12 weeks ||

369:       unlockTime > block.timestamp + 104 weeks ||

395:     if (comStake.unlockTime > block.timestamp) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-5"></a>[L-5] Prevent accidentally burning tokens
Minting and burning tokens to address(0) prevention

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

519:       if (!token.transfer(burnAddress, amountToBurn)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-6"></a>[L-6] Solidity version 0.8.20+ may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*Instances (1)*:
```solidity
File: contracts/IdentityStaking.sol

2: pragma solidity ^0.8.23;

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-7"></a>[L-7] Unsafe ERC20 operation(s)

*Instances (5)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

301:     if (!token.transfer(msg.sender, amount)) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

408:     if (!token.transfer(msg.sender, amount)) {

519:       if (!token.transfer(burnAddress, amountToBurn)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-8"></a>[L-8] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*Instances (7)*:
```solidity
File: contracts/IdentityStaking.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

6: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

7: import {PausableUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/PausableUpgradeable.sol";

19:   UUPSUpgradeable,

20:   AccessControlUpgradeable,

21:   PausableUpgradeable

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="L-9"></a>[L-9] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (11)*:
```solidity
File: contracts/IdentityStaking.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

6: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

7: import {PausableUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/PausableUpgradeable.sol";

19:   UUPSUpgradeable,

20:   AccessControlUpgradeable,

21:   PausableUpgradeable

172:   function initialize(

178:   ) public initializer {

183:     __AccessControl_init();

184:     __Pausable_init();

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Contracts are vulnerable to fee-on-transfer accounting-related issues | 2 |
| [M-2](#M-2) | Centralization Risk for trusted owners | 5 |
| [M-3](#M-3) | Return values of `transfer()`/`transferFrom()` not checked | 5 |
| [M-4](#M-4) | Unsafe use of `transfer()`/`transferFrom()` with `IERC20` | 5 |
### <a name="M-1"></a>[M-1] Contracts are vulnerable to fee-on-transfer accounting-related issues
Consistently check account balance before and after transfers for Fee-On-Transfer discrepancies. As arbitrary ERC20 tokens can be used, the amount here should be calculated every time to take into consideration a possible fee-on-transfer or deflation.
Also, it's a good practice for the future of the solution.

Use the balance before and after the transfer to calculate the received amount instead of assuming that it would be equal to the amount passed as a parameter. Or explicitly document that such tokens shouldn't be used and won't be supported

*Instances (2)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="M-2"></a>[M-2] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (5)*:
```solidity
File: contracts/IdentityStaking.sol

206:   function pause() external onlyRole(PAUSER_ROLE) whenNotPaused {

211:   function unpause() external onlyRole(PAUSER_ROLE) whenPaused {

218:   function _authorizeUpgrade(address) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}

429:   ) external onlyRole(SLASHER_ROLE) whenNotPaused {

540:   ) external onlyRole(RELEASER_ROLE) whenNotPaused {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="M-3"></a>[M-3] Return values of `transfer()`/`transferFrom()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

*Instances (5)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

301:     if (!token.transfer(msg.sender, amount)) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

408:     if (!token.transfer(msg.sender, amount)) {

519:       if (!token.transfer(burnAddress, amountToBurn)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)

### <a name="M-4"></a>[M-4] Unsafe use of `transfer()`/`transferFrom()` with `IERC20`
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.  For example Tether (USDT)'s `transfer()` and `transferFrom()` functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to `IERC20`, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see [this](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) link for a test case). Use OpenZeppelin's `SafeERC20`'s `safeTransfer()`/`safeTransferFrom()` instead

*Instances (5)*:
```solidity
File: contracts/IdentityStaking.sol

252:     if (!token.transferFrom(msg.sender, address(this), amount)) {

301:     if (!token.transfer(msg.sender, amount)) {

343:     if (!token.transferFrom(msg.sender, address(this), amount)) {

408:     if (!token.transfer(msg.sender, amount)) {

519:       if (!token.transfer(burnAddress, amountToBurn)) {

```
[Link to code](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)
