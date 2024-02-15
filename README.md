# Modular zkSync Native AA Wallet with Increment Finance Limit Order Module

## Overview

This repository contains a modular zkSync native Account Abstraction wallet with a module for executing limit orders on Increment Finance, as well as a paymaster-as-a-service called GasPond.

This repo is based on a [submission to the zkSync Era Hack0](https://app.buidlbox.io/projects/nongaswap) by [porco-rosso-j](https://github.com/porco-rosso-j), which won the Account Abstraction & Security sponsr prize from zkSync.

Contract: [`Account.sol`](https://github.com/webthethird/zksync-account-trade-limit/blob/5903181b50b369df3d22de9e3501cd16075bbf09/src/aa-wallet/Account.sol) & [`AccountFactory.sol`](https://github.com/webthethird/zksync-account-trade-limit/blob/5903181b50b369df3d22de9e3501cd16075bbf09/src/aa-wallet/AccountFactory.sol)

## Account Trade Limit & Swap Module

Modules serve as peripheral helpers and give AA-accounts extensional functionalities and protective limitations.

This project has a module called <INSERT MODULE NAME AND DESCRIPTION HERE>

### LimitOrderModule (tbd)

## GasPond(Paymaster)

Contract: [`GasPond.sol`](https://github.com/webthethird/zksync-account-trade-limit/blob/main/src/aa-wallet/paymaster/GasPond.sol)

A Paymaster contract called GasPond allows both free transactions and gas payments in ERC20 for accounts. GasPond also supports accounts' transactions via modules such as SwapModule if enabled.

### Sponsor Model

GasPond employs the sponsor model instead of the general paymaster model, meaning that the deployer/owner of the GasPond contract isn't a "paymaster" who compensates gas costs and accepts ERC20 gas payments. Rather, GasPond is a Paymaster-as-a-Service: any individual and project can become a sponsor on GasPond to sponsor users with easy registration, ETH deposit, and configurations.

This model could drastically reduce the cost of providing meta-transactions so that crypto services can improve the UX of their products more easily. Additionally, users don't have to rely on multiple different paymaster contracts where vulnerabilities and malicious bugs might exist.

### An example of the use of GasPond for a project

A project called XYZinc becomes a sponsor on GasPond, aiming to increase trading activities and awareness of their native token XYZ. As such, for purchases of XYZ tokens on DEXs supported by SwapModule, it accepts gas payments paid in XYZ with a 50% discount.

## Multicall

Contract: [`Multicall`](https://github.com/webthethird/zksync-account-trade-limit/blob/main/src/aa-wallet/libraries/Multicall.sol)

Account inherits Multicall contract that allows the account to perform both call and delegatecall in the same transaction. It also supports call/delegatecall to module contracts so that the account can combine any arbitrary logic in modules with methods in external contracts.

## Deployment

Deploy and configure all the contracts on zkSync local network and try features available with Account Abstraction Wallet and its module.

### Setup & Install dependencies

```shell
git clone git@github.com:webthethird/zksync-account-trade-limit.git
cd zksync-account-trade-limit
yarn
```

### Run zkSync local network

To set up a local environment, Docker and docker-compose should be installed.
If they are not installed on your computer: [Install](https://docs.docker.com/get-docker/).

```shell
git clone https://github.com/matter-labs/local-setup.git
cd local-setup
./start.sh
```

### Compile & Deploy contracts

Before, create `.env` file and add the line `NODE_ENV="local"`.

```shell
yarn hardhat compile
yarn hardhat deploy-zksync --script deploy/deployAll.ts
```

The deploy script above deploys the all necessary contracts and carry out initialization/configuration for each contract. For more details on how the deployment proceeds, look [`./deploy/..`](https://github.com/webthethird/zksync-account-trade-limit/tree/main/deploy) folder.

The output would look like the following.

```shell
weth: "0xD49036D56f474152891D9eced770D6b90B2cEAE9",
dai: "0xb1Ca5B44ef3627A3E5Ed7a6EE877D9D997A7c7ED",
lusd: "0x7AddC93ED39C4c64dffB478999B45f5a40619C23",
factory: "0xd19449266F443e67175e7669be788F94ca6e886e",
router: "0x13706Afd344d905BB9Cb50752065a67Fa8d09c70",
oracle: "0x4cf2E778D384746EaB115b914885e2bB18E893E2",
registry: "0xb47A53D7f201A7F4CA6FDBd1Efb083A041713101",
moduleManager: "0xd5608cEC132ED4875D19f8d815EC2ac58498B4E5",
swapModuleBase: "0x5A6be02aC21339d38cF0682A77bb24D858902246",
swapModule: "0xB3A273E27D718aadAa7895Ee0eD5B61e89219543",
aafactory: "0xE13BFf7F138853F7c33e6824Ea102D8b5cFe6bC8",
account: "0xD118052dF04aA7af089303a5889D2cE2F205627F",
gaspond: "0xdaa47000ab1013592C87c0FCf349aBE90178d2B8",
multicall1: "0xcFEbe41427dB860B7760507f50F39370e27e9D61",
multicall2: "0x8A1215E77D2ea1ce759a6bB0366870B21548F502",
```

Copy & paste all these deployed addresses into [`address.ts`](https://github.com/webthethird/zksync-account-trade-limit/blob/main/frontend/src/common/address.ts) and only the three of token addresses into [`tokenlist.json`](https://github.com/webthethird/zksync-account-trade-limit/blob/main/frontend/src/components/Modal/token_list.json).

### Setup & Run frontend

```shell
cd frontend
yarn
yarn start
```

### Connect Wallet & Account

Tap "Connect to a wallet" button at the top right in the UI and click it again. Then it shows you the account setting popup like in the image below.

Put the deployed account address (`0xD1180...`) into the field below `"Connect With Contract Account"` button and click it. If successuful, the connnected address on UI will be changed to the account address.

<p align="center">
<img width="500" alt="Screen Shot 2023-03-12 at 7 35 06" src="https://user-images.githubusercontent.com/88586592/224514450-c43ba27d-68be-4e6b-b6df-2f0307abc6b1.png">
<p/>

After the AA-wallet is connected, the information of Account trade Limit will be shown at the bottom.

<p align="center">
<img width="500" alt="Screen Shot 2023-03-12 at 7 50 45" src="https://user-images.githubusercontent.com/88586592/224514906-22739ff8-ff94-4dde-a087-ad1a5e7909e7.png">
<p/>

Account only owns DAI, neither ETH, WETH nor LUSD. But swapping DAI for ETH will succeed because multicall feature abstracts the approval process and gas fee is paid in DAI through GasPond.

So, please put the amount you want to swap. As you can see in the demo above, several limitations and warnings by SwapModule will be shown in `Account Trade Limit` section. Swap succeeds unless it violates those imposed restrictions.

## Question & Feedback (update this section for Increment when ready)

Discord: Porco#3106  
Twitter: [@porco_rosso_j](https://twitter.com/porco_rosso_j)
