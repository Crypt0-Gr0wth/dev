## Parcours français

Ce dépôt est accompagné d’un parcours documentaire en français consacré à Liquity. Voir [docs/fr/](docs/fr/).

# Liquity: Decentralized Borrowing Protocol


[<img alt="DeFiSafety Badge" width="96px" align="right" src="images/defisafety-badge.png" />](https://www.defisafety.com/app/pqrs/376)


[![Tests](https://github.com/liquity/dev/actions/workflows/test-contracts.yml/badge.svg)](https://github.com/liquity/dev/actions) [![Frontend status](https://img.shields.io/uptimerobot/status/m784948796-056b56fd51c67d682c11bb24?label=Testnet&logo=nginx&logoColor=white)](https://devui.liquity.org) ![uptime](https://img.shields.io/uptimerobot/ratio/7/m784948796-056b56fd51c67d682c11bb24) [![Discord](https://img.shields.io/discord/700620821198143498?label=join%20chat&logo=discord&logoColor=white)](https://discord.gg/2up5U32) [![Docker Pulls](https://img.shields.io/docker/pulls/liquity/dev-frontend?label=dev-frontend%20pulls&logo=docker&logoColor=white)](https://hub.docker.com/r/liquity/dev-frontend) [![codecov](https://codecov.io/gh/liquity/dev/branch/add_codecov/graph/badge.svg)](https://codecov.io/gh/liquity/dev)




Liquity is a decentralized protocol that allows Ether holders to obtain maximum liquidity against
their collateral without paying interest. After locking up ETH as collateral in a smart contract and
creating an individual position called a "trove", the user can get instant liquidity by minting LUSD,
a USD-pegged stablecoin. Each trove is required to be collateralized at a minimum of 110%. Any
owner of LUSD can redeem their stablecoins for the underlying collateral at any time. The redemption
mechanism along with algorithmically adjusted fees guarantee a minimum stablecoin value of USD 1.


An unprecedented liquidation mechanism based on incentivized stability deposits and a redistribution
cycle from riskier to safer troves provides stability at a much lower collateral ratio than current
systems. Stability is maintained via economically-driven user interactions and arbitrage, rather
than by active governance or monetary interventions.


The protocol has built-in incentives that encourage both early adoption and the operation of
multiple front ends, enhancing decentralization.


## More information


Visit [liquity.org](https://www.liquity.org) to find out more and join the discussion.


## Liquity System Summary


- [Disclaimer](#disclaimer)
- [Liquity Overview](#liquity-overview)
- [Liquidation and the Stability Pool](#liquidation-and-the-stability-pool)
  - [Liquidation gas costs](#liquidation-gas-costs)
  - [Liquidation Logic](#liquidation-logic)
    - [Liquidations in Normal Mode: TCR >= 150%](#liquidations-in-normal-mode-tcr--150)
    - [Liquidations in Recovery Mode: TCR < 150%](#liquidations-in-recovery-mode-tcr--150)
- [Gains From Liquidations](#gains-from-liquidations)
- [LUSD Token Redemption](#lusd-token-redemption)
  - [Partial redemption](#partial-redemption)
  - [Full redemption](#full-redemption)
  - [Redemptions create a price floor](#redemptions-create-a-price-floor)
- [Recovery Mode](#recovery-mode)
- [Project Structure](#project-structure)
  - [Directories](#directories)
  - [Branches](#branches)
- [LQTY Token Architecture](#lqty-token-architecture)
  - [LQTY Lockup contracts and token vesting](#lqty-lockup-contracts-and-token-vesting)
  - [Lockup Implementation and admin transfer restriction](#lockup-implementation-and-admin-transfer-restriction)
  - [Launch sequence and vesting process](#launch-sequence-and-vesting-process)
    - [Deploy LQTY Contracts](#deploy-lqty-contracts)
    - [Deploy and fund Lockup Contracts](#deploy-and-fund-lockup-contracts)
    - [Deploy Liquity Core](#deploy-liquity-core)
    - [During one year lockup period](#during-one-year-lockup-period)
    - [Upon end of one year lockup period](#upon-end-of-one-year-lockup-period)
    - [Post-lockup period](#post-lockup-period)
- [Core System Architecture](#core-system-architecture)
  - [Core Smart Contracts](#core-smart-contracts)
  - [Data and Value Silo Contracts](#data-and-value-silo-contracts)
  - [Contract Interfaces](#contract-interfaces)
  - [PriceFeed and Oracle](#pricefeed-and-oracle)
  - [PriceFeed Logic](#pricefeed-logic)
  - [Testnet PriceFeed and PriceFeed tests](#testnet-pricefeed-and-pricefeed-tests)
  - [PriceFeed limitations and known issues](#pricefeed-limitations-and-known-issues)
  - [Keeping a sorted list of Troves ordered by ICR](#keeping-a-sorted-list-of-troves-ordered-by-icr)
  - [Flow of Ether in Liquity](#flow-of-ether-in-liquity)
  - [Flow of LUSD tokens in Liquity](#flow-of-lusd-tokens-in-liquity)
  - [Flow of LQTY Tokens in Liquity](#flow-of-lqty-tokens-in-liquity)
- [Expected User Behaviors](#expected-user-behaviors)
- [Contract Ownership and Function Permissions](#contract-ownership-and-function-permissions)
- [Deployment to a Development Blockchain](#deployment-to-a-development-blockchain)
- [Running Tests](#running-tests)
  - [Brownie Tests](#brownie-tests)
  - [OpenEthereum](#openethereum)
  - [Coverage](#coverage)
- [System Quantities - Units and Representation](#system-quantities---units-and-representation)
  - [Integer representations of decimals](#integer-representations-of-decimals)
- [Public Data](#public-data)
- [Public User-Facing Functions](#public-user-facing-functions)
  - [Borrower (Trove) Operations - `BorrowerOperations.sol`](#borrower-trove-operations---borroweroperationssol)
  - [TroveManager Functions - `TroveManager.sol`](#trovemanager-functions---trovemanagersol)
  - [Hint Helper Functions - `HintHelpers.sol`](#hint-helper-functions---hinthelperssol)
  - [Stability Pool Functions - `StabilityPool.sol`](#stability-pool-functions---stabilitypoolsol)
  - [LQTY Staking Functions  `LQTYStaking.sol`](#lqty-staking-functions--lqtystakingsol)
  - [Lockup Contract Factory `LockupContractFactory.sol`](#lockup-contract-factory-lockupcontractfactorysol)
  - [Lockup contract - `LockupContract.sol`](#lockup-contract---lockupcontractsol)
  - [LUSD token `LUSDToken.sol` and LQTY token `LQTYToken.sol`](#lusd-token-lusdtokensol-and-lqty-token-lqtytokensol)
- [Supplying Hints to Trove operations](#supplying-hints-to-trove-operations)
  - [Hints for `redeemCollateral`](#hints-for-redeemcollateral)
