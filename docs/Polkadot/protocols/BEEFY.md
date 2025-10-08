---
title: BEEFY
---

## Or the rise of decentralised blockchain bridges: Say goodbye to trusted intermediaries
![](BEEFY-bridges.png)

This overview blog accompanies a recently accepted research paper titled “[Trustless Bridges via Random Sampling Light Clients](https://eprint.iacr.org/2025/057.pdf)” at [Advances in Financial Technologies](https://advfintech.org/aft25/program.html) (AFT) 2025.

The blockchain landscape is a collection of decentralised networks, each operating as its own "island" with specific features and protocols that define its ecosystem. This setup exists because no single architecture can scale to serve all possible users, and because different security philosophies have led to diverse systems. One advantage of this separation is that it reduces risk: a security failure in one system does not compromise all users. It also makes upgrades and security audits more manageable. While this isolation enhances resilience, it also creates fragmentation, making it difficult for assets, data, and users to move freely between chains, an aspect necessary for usability and for applications that depend on input from other blockchains. 

Interoperability refers precisely to the ability of blockchains to communicate and interact with one another. Hitherto, there has been a lack of secure and efficient solutions that enable interoperability. As a result, centralised bridges introducing trusted intermediaries have emerged, forcing users to rely on their services. 

The core issue with these intermediaries is that their very existence contradicts two fundamental pillars on which blockchain technology is grounded: decentralisation and trust minimisation. Intermediaries, such as multisignature relayers, currently facilitate interoperability between blockchain networks by validating information exchanged across chains. Yet this process inherently centralises information. It also introduces additional trust assumptions, requiring users to believe that these centralised entities will handle their data ethically and refrain from using it for other purposes. 

Another drawback to consider is security, as this setup creates a single point of failure, making the system vulnerable to various attack vectors. One such vector is censorship, which is easy to implement, difficult to detect, and likely to compromise overall security. 

Indeed, recent history underscores the risks intrinsic in this setup, where centralised entities have been compromised or have even acted maliciously. According to [Chainalysis](https://www.chainalysis.com/blog/cross-chain-bridge-hacks-2022/), failures in centralised bridges account for over 60% of all crypto hacks, resulting in losses exceeding $2 billion. Moreover, the [Rekt leaderboard](https://rekt.news/tr/leaderboard) shows that four of the top five incidents involve bridge-related hacks.

The missing piece of the puzzle in this context is decentralised bridges, which are essential components for upholding the philosophy of blockchain technology by allowing users to move freely across different networks. Just as bridges in the physical world connect locations, individuals, and economies, the essence of connection should be similar in the digital realm. Blockchain bridges are not merely technical mechanisms, they are vital infrastructure enabling cross-chain communication between technically distinct networks such as Polkadot and Ethereum. Through these bridges, networks can obtain and verify each other’s finalised states, empowering assets, data, and ecosystems, igniting collaboration across diverse digital platforms. 

The Web3 Foundation takes this issue seriously, and its research team has been actively involved in making interoperability a core feature of Polkadot. These efforts have led to the design of Random Sampling BEEFY, an efficient, decentralised, and secure bridge protocol for Proof-of-Stake blockchains. 

## Random Sampling BEEFY and its architecture

Given the security and reliability concerns associated with centralised interoperability solutions, we set out to design a trustless bridge protocol between Proof-of-Stake blockchains.  

What does a bridge mean in this context? Consider two independent blockchains: X (for example, Polkadot) and Y (for example, Ethereum). A bridge protocol from source X to destination Y enables Y to fetch the latest finalised state root (a commitment to the state) of X and verify its finality. This core functionality allows on-chain logic on Y to access and interpret the latest finalised state of X. Such a simple concept unlocks applications like swaps, asset transfers, cross-chain governance, and more.   

And what about trustless? The security guarantees of the protocol should not depend on any additional trust assumptions beyond the pre-existing honesty assumptions of the underlying chains being connected. For example, there is no assumption regarding the existence of honest relayers (i.e., parties exchanging messages). This means the protocol relies only on the existing assumptions, such as Byzantine fault tolerance, which is already embedded in the connected chains. 

A trustless bridge design typically involves running a light client on the target chain that stores block headers from the source chain. This allows the target chain to verify specific states or transactions by checking Merkle proofs against the state roots in those headers. The problem with this setup is that on-chain computation, especially on Ethereum, is scarce and costly. 

To address this, Web3 Foundation researchers designed a light-client protocol that enables an Ethereum smart contract to track Polkadot’s finality. BEEFY, a consensus layer wrapped around [GRANDPA](https://research.web3.foundation/Polkadot/protocols/finality), reduces the light client’s workload by eliminating the need to track forks or GRANDPA justifications. Once a BEEFY vote is completed, where all honest validators sign off on a GRANDPA-finalised block, the light client only needs to verify that the block has received enough BEEFY votes. Efficiency can be further enhanced by using [Merkle Mountain Ranges](https://github.com/opentimestamps/opentimestamps-server/blob/master/doc/merkle-mountain-range.md) proofs for aggregable state commitments and [ECDSA signatures](https://eprint.iacr.org/2020/1390.pdf), which are more gas-efficient on the Ethereum Virtual Machine (EVM).

Despite BEEFY’s simplifications, verifying 401 signatures per update (given Polkadot’s ~600 validators) remains expensive. This cost is reduced by implementing an interactive protocol using the [RANDAO](https://github.com/randao/randao) randomness beacon. The protocol unfolds in three phases: commit, challenge, and response. In the commit phase, a relayer submits a state commitment and claims to have a supermajority of validator signatures, providing one signature that can be slashed if fraudulent. During the challenge phase, the light client uses RANDAO to randomly select around 25 signatures from the claimed list. In the response phase, the relayer submits exactly those sampled signatures for verification. If they check out, the finalised block is accepted.

![](BEEFY-process.png)

This strategy drastically reduces the number of required checks and, more importantly, is independent of the size of the validator set on the source chain. The subsample size is a critical parameter that balances security and efficiency. It is derived from crypto-economic reasoning to ensure that the expected incentive to attack the bridge remains negative (model details are omitted for exposition). In concrete terms, for a source-chain validator set of 10^4, the gas cost of verification would be approximately 79,000 gwei using Random Sampling BEEFY, compared to roughly 23 million gwei with the naive approach and around 819,000 gwei using [Ultra-PLONK](https://github.com/personaelabs/spartan-ecdsa) for ECDSA signature verification. 

Random Sampling BEEFY currently powers [Snowbridge](https://app.snowbridge.network/), a decentralised bridge connecting Polkadot and Ethereum. Launched in June 2024, Snowbridge has been growing rapidly and now boasts over $70 million in total value locked (TVL). Parachains like [Mythos](https://mythos.foundation/) and [Hydration](https://hydration.net/) are already leveraging its capabilities, so let’s take a closer look at BEEFY in action. 

## Snowbridge: Random Sampling BEEFY in Action 

The community-owned, trustless bridge **Snowbridge** allows users to securely transfer assets between Polkadot and Ethereum without relying on multisignatures or intermediaries. Under the hood, Snowbridge is secured by the Random Sampling BEEFY protocol, which provides crypto-economic security guarantees. This bridge has been able to support projects that require cross-chain interoperability, secure transfers, and decentralised infrastructure. The following examples highlight its range of capabilities. 

**Mythical Games’ migration**. Snowbridge played a fundamental role in powering the migration of [Mythical Games](https://mythicalgames.com/), enabling players and developers to transfer tokens from Ethereum to Mythos on Polkadot. Without this bridge, and relying on intermediaries as was necessary prior to Snowbridge’s launch, the migration would have been extremely complex and time-consuming.   

**Hydration’s campaign**. Snowbridge supported [Hydration’s “GigaHydration”](https://app.hydration.net/trade/swap) campaign by enabling users to bridge tBTC, wstETH, stablecoins (USDC, USDT), LDO, and SKY into Polkadot to join omnipools, earn yield, and participate in farming. This seamless asset transfer allowed users to engage with Hydration’s DeFi ecosystem while maintaining the same security and decentralisation of their native chains. Snowbridge, and hence Random Sampling BEEFY, played a key role in boosting liquidity and driving user participation across the campaign by reducing friction and enhancing accessibility.   

Beyond these integrations, Snowbridge unlocks new possibilities for DeFi, gaming, and governance across the Polkadot network by connecting Ethereum with Polkadot and its parachains. This broad connectivity empowers developers and users to transfer assets across diverse ecosystems with ease and security. Snowbridge is expected to experience significant growth in the coming years as adoption accelerates. Being a key driver of the Polkadot ecosystem, Snowbridge facilitates cross-chain use cases and brings valuable liquidity from Ethereum to Polkadot.

## Key takeaways

Decentralised bridges enable a wide range of applications, including asset swaps and chain migrations. As such, their security is essential to ensure sustainable growth. By adopting Random Sampling BEEFY, cross-chain communication becomes more secure and efficient than ever. At the same time, it pushes the boundaries of what’s possible with trustless bridges. 


One key advantage of Random Sampling BEEFY is its ability to reduce operational costs without compromising bridge security. This is a technology that reflects the Web3 Foundation’s strong commitment to secure, decentralised systems, perfectly aligning with our motto: **“Less Trust, More Truth”**. 

Random Sampling BEEFY fuels Snowbridge, which will become a cornerstone of Web3 infrastructure. Its trustless architecture ensures secure and decentralised asset transfers, reducing reliance on centralised bridges and enhancing the resilience of the multi-chain landscape. With growing interest in interoperable DeFi, NFTs, and AI-powered applications, Polkadot is ready to support the next wave of innovation across the ecosystem. 

**For more information or if you have any questions, please contact** [Bhargav Nagaraja Bhatt](/team_members/Bhargav.md)
