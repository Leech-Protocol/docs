---
label: Smart Contract
order: 97
---


The smart contract is responsible for the implementation of the Yield aggregation strategy and its interaction with DeFi platforms according to the Strategy. It includes:
- Provide Liquidity to DEXes (Minting LP tokens)
- Stake LPs for earning rewards
- Claim and compound rewards
- Swap and supply to the Lending and borrowing platforms

As for the cross-chain, when users request cross-chain depositing and withdrawing, LeechProtocol bridges the underlying assets with a third party bridge. The currently used bridge is Stargate. After this the assets are sent to the smart contract for further interaction with DEXes.
