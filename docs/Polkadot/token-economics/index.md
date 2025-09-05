---
title: Token Economics
---

import DocCardList from '@theme/DocCardList';


![](polkadot-token.png)


Polkadot is a proof-of-stake–based platform in which a set of validators, who have staked DOT[^1], produce blocks and reach consensus. Polkadot validators are rewarded roughly in proportion to their staked amount, unless they deviate from the protocol, in which case a portion of their DOT is slashed.

On this platform, the set of nodes elected as validators changes constantly in each era, approximately once a day, although their total number remains limited. Any number of DOT holders can participate indirectly in the decision-making processes as *nominators*, under a system known as *nominated proof-of-stake*. A nominator selects validator candidates they trust and stakes DOT to support their nomination. If one or more of their nominated candidates are elected as validators in an era, the nominator shares with them any economic reward or penalty, proportional to their stake. 

Being a nominator is a way to invest one's DOT while contributing to the security of the system. The greater the total amount of DOT staked by nominators and validators, the higher the system’s security—since an adversary would require significantly more stake, or nominators' trust, to succeed in getting nodes elected as validators.

The aim, therefore, is for validators and nominators to stake a considerable percentage of the total DOT supply. Another significant portion of the DOT supply will be locked as deposits by commercial blockchains that secure parachain slots. 

The following sections focus on three key topics. **NPoS payment and inflation** explains how well-behaved validators and nominators are rewarded under Polkadot’s nominated proof-of-stake system. **Transaction fees** examines optimal fee structures on the relay chain to cover operational costs, mitigate harmful behavior, and manage periods of high activity or delayed transaction inclusion. Finally, **Treasury** discusses how and when may DOT be raised to support ongoing network maintenance. The closing paragraph provides links to further resources related to the Polkadot protocol.

[^1]: DOT are Polkadot's native token and their main functions are: 1) Economics: Polkadot mints or burns DOT to reward the nodes that run the consensus protocol, fund the treasury, control the inflation rate, and support other economics mechanisms. 2) Slashing: DOT also play a role in slashing protocols designed to desincentivize attacks or adversarial behaviors. 3) Governance: DOT function as voting power, allowing DOT holders to express their opinion in governance decisions via referenda. 4) Parachain allocation: Finally, DOT are used to decide which projects receive parachain slots, through auctions and deposits. This section focuses on the first function, while the remaining three are explored in subsequent sections.


<DocCardList />
