---
label: Separate vaults
order: 95
---

Leech Protocol allows users to easily farm on multiple chains. They deposit their
liquidity into the Leech Protocol, which then performs operations that the user would normally do manually. 

Depending on the type of strategy assigned to the vault - we distribute the user's deposit into liquidity pairs on Dexes. The deposit is splitted according to the shares necessary for the pair on DeX we work with, and then swapped and added as liquidity onto DeX. Next we receive rewards from the DeX, swap them into necessary tokens from the pair we work with and return them into the deposit to get more rewards.

With each swap, the amount of the deposit body can slightly change due to slippage. However, in the long term this deposit is growing as well as the user’s profit.

How will it work? The user chooses a main asset, such as USDT or USDC, and deposits liquidity. Our system swaps liquidity, bridges it (if needed), and distributes it into a defined pool list. For instance, if a user picks USDT as their main asset, the system bridges it, swaps liquidity to different pools like USDT/USDC, USDC/MAI, ETH/wUSDR, etc.

As for now we offer the following separate vaults for deposit/withdraw with the following networks:

     Vault Name: USDT
     Network: Avalanche
     DEX: Yak (USDT (Aave) pool)
     Address: 0xE0a7D8B869590Cc4CC35b7E1ae66A5f3c03255D3


     Vault Name: USDC
     Network: BNB Chain
     DEX: Venus (USDC pool)
     Address: 0x980B9CA3a9Ba386a34bbFF773C87f2884Fb57742


     Vault Name: USDT/USDC 
     Network: BNB Chain
     DEX: Biswap (USDT/USDC pool)
     Address: 0xeB7355898247F4f652369CB20F537ba5ec9ED7BE
