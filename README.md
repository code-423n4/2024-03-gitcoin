
# Repo setup

## ⭐️ Sponsor: Add code to this repo

- [ ] Create a PR to this repo with the below changes:
- [ ] Provide a self-contained repository with working commands that will build (at least) all in-scope contracts, and commands that will run tests producing gas reports for the relevant contracts.
- [ ] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 48 business hours prior to audit start time.**
- [ ] Be prepared for a 🚨code freeze🚨 for the duration of the audit — important because it establishes a level playing field. We want to ensure everyone's looking at the same code, no matter when they look during the audit. (Note: this includes your own repo, since a PR can leak alpha to our wardens!)


---

## ⭐️ Sponsor: Edit this `README.md` file

- [ ] Modify the contents of this `README.md` file. Describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. (Here are two well-constructed examples: [Ajna Protocol](https://github.com/code-423n4/2023-05-ajna) and [Maia DAO Ecosystem](https://github.com/code-423n4/2023-05-maia))
- [ ] Review the Gas award pool amount. This can be adjusted up or down, based on your preference - just flag it for Code4rena staff so we can update the pool totals across all comms channels.
- [ ] Optional / nice to have: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] [This checklist in Notion](https://code4rena.notion.site/Key-info-for-Code4rena-sponsors-f60764c4c4574bbf8e7a6dbd72cc49b4#0cafa01e6201462e9f78677a39e09746) provides some best practices for Code4rena audits.

## ⭐️ Sponsor: Final touches
- [ ] Review and confirm the details in the section titled "Scoping details" and alert Code4rena staff of any changes.
- [ ] Review and confirm the list of in-scope files in the `scope.txt` file in this directory.  Any files not listed as "in scope" will be considered out of scope for the purposes of judging, even if the file will be part of the deployed contracts.
- [ ] Check that images and other files used in this README have been uploaded to the repo as a file and then linked in the README using absolute path (e.g. `https://github.com/code-423n4/yourrepo-url/filepath.png`)
- [ ] Ensure that *all* links and image/file paths in this README use absolute paths, not relative paths
- [ ] Check that all README information is in markdown format (HTML does not render on Code4rena.com)
- [ ] Remove any part of this template that's not relevant to the final version of the README (e.g. instructions in brackets and italic)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Gitcoin audit details
- Total Prize Pool: $14,000 in USDC
  - HM awards: $7,740 in USDC
  - Analysis awards: $430 in USDC
  - QA awards: $215 in USDC
  - Gas awards: $215 in USDC
  - Judge awards: $1,900 in USDC
  - Scout awards: $500 in USDC
  - Mitigation Review: $3,000 in USDC (*Opportunity goes to top 3 certified wardens based on placement in this audit.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-03-gitcoin/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts March 6, 2024 20:00 UTC
- Ends March 12, 2024 20:00 UTC

## This is a Private audit

This audit repo and its Discord channel are accessible to **certified wardens only.** Participation in private audits is bound by:

1. Code4rena's [Certified Contributor Terms and Conditions](https://github.com/code-423n4/code423n4.com/blob/main/_data/pages/certified-contributor-terms-and-conditions.md)
2. C4's [Certified Contributor Code of Professional Conduct](https://code4rena.notion.site/Code-of-Professional-Conduct-657c7d80d34045f19eee510ae06fef55)

*All discussions regarding private audits should be considered private and confidential, unless otherwise indicated.*

Please review the following confidentiality requirements carefully, and if anything is unclear, ask questions in the private audit channel in the C4 Discord.

>>DRAG IN CLASSIFIED IMAGE HERE

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-03-gitcoin/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

[ ⭐️ SPONSORS: Are there any known issues or risks deemed acceptable that shouldn't lead to a valid finding? If so, list them here. ]


# Overview

[ ⭐️ SPONSORS: add info here ]

## Links

- **Previous audits:** 
- **Documentation:**
- **Website:**
- **Twitter:** 
- **Discord:** 


# Scope

[ ⭐️ SPONSORS: add scoping and technical details here ]

- [ ] In the table format shown below, provide the name of each contract and:
  - [ ] source lines of code (excluding blank lines and comments) in each *For line of code counts, we recommend running prettier with a 100-character line length, and using [cloc](https://github.com/AlDanial/cloc).* 
  - [ ] external contracts called in each
  - [ ] libraries used in each

*List all files in scope in the table below (along with hyperlinks) -- and feel free to add notes here to emphasize areas of focus.*

| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [contracts/folder/sample.sol](https://github.com/code-423n4/repo-name/blob/contracts/folder/sample.sol) | 123 | This contract does XYZ | [`@openzeppelin/*`](https://openzeppelin.com/contracts/) |

## Out of scope

*List any files/contracts that are out of scope for this audit.*

# Additional Context

- [ ] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [ ] Please list specific ERC20 that your protocol is anticipated to interact with. Could be "any" (literally anything, fee on transfer tokens, ERC777 tokens and so forth) or a list of tokens you envision using on launch.
- [ ] Please list specific ERC721 that your protocol is anticipated to interact with.
- [ ] Which blockchains will this code be deployed to, and are considered in scope for this audit?
- [ ] Please list all trusted roles (e.g. operators, slashers, pausers, etc.), the privileges they hold, and any conditions under which privilege escalation is expected/allowable
- [ ] In the event of a DOS, could you outline a minimum duration after which you would consider a finding to be valid? This question is asked in the context of most systems' capacity to handle DoS attacks gracefully for a certain period.
- [ ] Is any part of your implementation intended to conform to any EIP's? If yes, please list the contracts in this format: 
  - `Contract1`: Should comply with `ERC/EIPX`
  - `Contract2`: Should comply with `ERC/EIPY`

## Attack ideas (Where to look for bugs)
*List specific areas to address - see [this blog post](https://medium.com/code4rena/the-security-council-elections-within-the-arbitrum-dao-a-comprehensive-guide-aa6d001aae60#9adb) for an example*

## Main invariants
*Describe the project's main invariants (properties that should NEVER EVER be broken).*

## Scoping Details 
[ ⭐️ SPONSORS: please confirm/edit the information below. ]

```
- If you have a public code repo, please share it here: https://github.com/gitcoinco/id-staking-v2  
- How many contracts are in scope?: 1    
- Total SLoC for these contracts?: 230  
- How many external imports are there?: 3  
- How many separate interfaces and struct definitions are there for the contracts within scope?: 1  
- Does most of your code generally use composition or inheritance?: Inheritance   
- How many external calls?: 8   
- What is the overall line coverage percentage provided by your tests?: 100
- Is this an upgrade of an existing system?: False
- Check all that apply (e.g. timelock, NFT, AMM, ERC20, rollups, etc.): Timelock function 
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?: False   
- Please describe required context:   
- Does it use an oracle?: No 
- Describe any novel or unique curve logic or mathematical models your code uses: 
- Is this either a fork of or an alternate implementation of another project?: False   
- Does it use a side-chain?:
- Describe any specific areas you would like addressed:
```

# Tests

*Provide every step required to build the project from a fresh git clone, as well as steps to run the tests with a gas report.* 

*Note: Many wardens run Slither as a first pass for testing.  Please document any known errors with no workaround.* 

## Miscellaneous

Employees of Gitcoin and employees' family members are ineligible to participate in this audit.
