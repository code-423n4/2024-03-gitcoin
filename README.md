# Gitcoin audit details

- Total Prize Pool: $14,000 in USDC
  - HM awards: $7,740 in USDC
  - Analysis awards: $430 in USDC
  - QA awards: $215 in USDC
  - Gas awards: $215 in USDC
  - Judge awards: $1,900 in USDC
  - Scout awards: $500 in USDC
  - Mitigation Review: $3,000 in USDC (_Opportunity goes to top 3 certified wardens based on placement in this audit._)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-03-gitcoin-passport-identity-staking-invitational/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts March 6, 2024 20:00 UTC
- Ends March 12, 2024 20:00 UTC

## This is a Private audit

This audit repo and its Discord channel are accessible to **certified wardens only.** Participation in private audits is bound by:

1. Code4rena's [Certified Contributor Terms and Conditions](https://github.com/code-423n4/code423n4.com/blob/main/_data/pages/certified-contributor-terms-and-conditions.md)
2. C4's [Certified Contributor Code of Professional Conduct](https://code4rena.notion.site/Code-of-Professional-Conduct-657c7d80d34045f19eee510ae06fef55)

_All discussions regarding private audits should be considered private and confidential, unless otherwise indicated._

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-03-gitcoin/blob/main/4naly3er-report.md).

_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

# Overview

The `IdentityStaking` protocol allows users to stake amount of tokens (GTC) and lock the stake for a specified duration. After the expiration of the locking period, the stake can be withdrawn or locked again for another period.
The stake is considered to be a gurantee of someones reputation. It is a colateral that users put as a gurantee on their or on someone elses reputation.
Users will be able to claim stamps in the Gitcoin Passport application based on the amount of tokens staked on them, and this stamps will count towards their humanity score.
Users who are misbehaving and trying to cheat the Gitcoin Passport mechanism to get higher humanity scores will be punished by having the respective stakes slashed.

The slashing logic is not part of the `IdentityStaking` protocol. The misbehaving users will be identified in an off-chain analysis, and the slashing will be executed by calling the `slash` function of the `IdentityStaking` protocol.

The rules for slashing:

- the slashing will be executed on any staked GTC even if the lock period has expired and the GTC has not yet been withdrawn
- if I have staked GTC on myself, and I have misbehaved, then my stake will be slashed
- if I have staked GTC on other people, and I have misbehaved, then all my stakes on others will be slashed
- if someone else has staked GTC on me, and I have misbehaved, then those particular stakes will be slashed

Slashed GTC will be held in the SC for at least a minimum appeal time (`burnRoundMinimumDuration`), during which time slashed users can appeal the slashing decision. Upon succefull appeal funds can be restored using the `release` function of the `IdentityStaking` protocol.

After the appeal time has ended, funds will be burned using the `lockAndBurn` function from the `IdentityStaking` protocol, which will transfer the funds to a designated burn address.

## Links

- **Previous audits:** na
- **Documentation:** the readme of the main [repozitory](https://github.com/gitcoinco/id-staking-v2)
- **Website:** this is the link to the old version of the DApp. The new contracts have been rewritten: [https://staking.passport.gitcoin.co/](https://staking.passport.gitcoin.co/)
- **Twitter:** [https://twitter.com/gitcoinpassport](https://twitter.com/gitcoinpassport)
- **Discord:** [https://discord.gg/gitcoin](https://discord.gg/gitcoin)

# Scope

- [x] In the table format shown below, provide the name of each contract and:
  - [ ] source lines of code (excluding blank lines and comments) in each _For line of code counts, we recommend running prettier with a 100-character line length, and using [cloc](https://github.com/AlDanial/cloc)._
  - [ ] external contracts called in each
  - [x] libraries used in each

---

| Contract                                                                                                                               | SLOC | Purpose                                                         | Libraries used    |
| -------------------------------------------------------------------------------------------------------------------------------------- | ---- | --------------------------------------------------------------- | ----------------- |
| [contracts/IIdentityStaking.sol](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IIdentityStaking.sol) | 3    | This contract implements interface for identity staking staking | `@openzeppelin/*` |
| [contracts/IdentityStaking.sol](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/IdentityStaking.sol)   | 297  | This contract implements identity staking                       | na                |

## Out of scope

_List any files/contracts that are out of scope for this audit._

---

| Contract                                                                                                                                     | SLOC | Purpose                   | Libraries used |
| -------------------------------------------------------------------------------------------------------------------------------------------- | ---- | ------------------------- | -------------- |
| [contracts/test_mocks/GTC.sol](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/test_mocks/GTC.sol)           | 205  | Mock contract for testing | na             |
| [contracts/test_mocks/SafeMath.sol](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/test_mocks/SafeMath.sol) | 52   | Mock contract for testing | na             |
| [contracts/test_mocks/Upgrade.sol](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/contracts/test_mocks/Upgrade.sol)   | 7    | Mock contract for testing | na             |

# Additional Context

An overview of the logic of identity staking has been covered in the **Overview** section above. Further details of how this was designed is covered in the [readme](./id-staking-v2/README.md) in the `id-staking-v2` folder in the sections:

1. [Appendix A: Slashing Rounds](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/README.md#appendix-a-slashing-rounds)
2. [Appendix B: Slashing in Consecutive Rounds](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/README.md#appendix-b-slashing-in-consecutive-rounds)
3. [Appendix C: Diagrams](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/README.md#appendix-c-diagrams)

Also gas optimisations and security aspects are outlined here:

1. [Appendix D: Security](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/README.md#appendix-d-security)

This contract will interact with the GTC ERC-20 contract, as the GTC token will be the token that will be staked. GTC is available on:

- ETH mainnet, the token contract address is: `0xde30da39c46104798bb5aa3fe8b9e0e1f348163f`
- Optimism, the token contract address is: `0x1EBA7a6a72c894026Cd654AC5CDCF83A46445B08`

The smart contract will be deployed to **ETH Mainnet** and **Optimism**.

The trusted roles are:

- DEFAULT_ADMIN_ROLE - owner of the SC, authorized to do upgrades
- PAUSER_ROLE - pause / unpause the smart contract
- SLASHER_ROLE - authorized to call the slash function
- RELEASER_ROLE - authorized to release funds frozen for slashing

The smart contract `IdentityStaking` does not need to comply to any EIP.

The compiler version configured is `^0.8.23` (this already uses unchedked loo increments, so we did not have to do explicit unchecked increments).

## Attack ideas (Where to look for bugs)

Our main concern is the safety of the funds in the smart contract:

- users can only withdraw what they deposited (minus the slashed amount)
- only users with SLASHER_ROLE role can slash
- only users RELEASER_ROLE can release
- only DEFAULT_ADMIN_ROLE can assign roles or upgrade the contract

_List specific areas to address - see [this blog post](https://medium.com/code4rena/the-security-council-elections-within-the-arbitrum-dao-a-comprehensive-guide-aa6d001aae60#9adb) for an example_

## Main invariants

_Describe the project's main invariants (properties that should NEVER EVER be broken)._

### Invariant 1 - total slashed in current or previous round

```
for round = currentRound or currentRound - 1

totalSlashed[round] = (sum of stake.slashedAmount for all selfStakes and communityStakes where stake.slashedInRound = round)
```

### Invariant 2 - userTotalStaked

```
userTotalStaked[address] = selfStakes[address].amount + sum(communityStakes[address][x].amount for all x staked on by this address)
```

## Scoping Details

```
- If you have a public code repo, please share it here: https://github.com/gitcoinco/id-staking-v2
- How many contracts are in scope?: 1
- Total SLoC for these contracts?: 300
- How many external imports are there?: 5
- How many separate interfaces and struct definitions are there for the contracts within scope?: 1
- Does most of your code generally use composition or inheritance?: Inheritance
- How many external calls?: 9
- What is the overall line coverage percentage provided by your tests?: 100
- Is this an upgrade of an existing system?: False
- Check all that apply (e.g. timelock, NFT, AMM, ERC20, rollups, etc.): Timelock function
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?: False
- Please describe required context:
- Does it use an oracle?: No
- Describe any novel or unique curve logic or mathematical models your code uses:
- Is this either a fork of or an alternate implementation of another project?: False
- Does it use a side-chain?: False
- Describe any specific areas you would like addressed: na
```

# Tests

_Provide every step required to build the project from a fresh git clone, as well as steps to run the tests with a gas report._

## Running tests

Clone and cd into the `id-staking-v2` folder in the repo:
`git clone https://github.com/code-423n4/2024-03-gitcoin.git && cd 2024-03-gitcoin/id-staking-v2`

Run the tests: `npx hardhat test`
Run the tests with gas report: `REPORT_GAS=true npx hardhat test`

## Slither warning

Slither will report dangerous comparison: `uses timestamp for comparisons` ([https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp](https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp))

This issue is known and considered acceptable as explained here: [Appendix D: Security](https://github.com/code-423n4/2024-03-gitcoin/blob/main/id-staking-v2/README.md#appendix-d-security)

## Miscellaneous

Employees of Gitcoin and employees' family members are ineligible to participate in this audit.
