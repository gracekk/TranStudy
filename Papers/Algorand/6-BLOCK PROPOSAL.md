6

BLOCK PROPOSAL

To ensure that some block is proposed in each round, Al- gorand sets the sortition threshold for the block-proposal role, τ proposer , to be greater than 1 (although Algorand will reach consensus on at most one of these proposed blocks). Appendix B proves that choosing τ proposer = 26 ensures that a reasonable number of proposers (at least one, and no more than 70, as a plausible upper bound) are chosen with very high probability (e.g., 1−10 −11 ).

Minimizing unnecessary block transmissions. One risk of choosing several proposers is that each will gossip their own proposed block. For a large block (say, 1 MByte), this can incur a significant communication cost. To reduce this cost, the sortition hash is used to prioritize block propos- als: For each selected sub-user 1,...,j of user i, the priority for the block proposal is obtained by hashing the (verifiably random) hash output of VRF concatenated with the sub-user index. The highest priority of all the block proposer’s se- lected sub-users is the priority of the block.

Algorand users discard messages about blocks that do not have the highest priority seen by that user so far. Algorand also gossips two kinds of messages: one contains just the priorities and proofs of the chosen block proposers (from sortition), and the other contains the entire block, which also includes the proposer’s sortition hash, and proof. The first kind of message is small (about 200 Bytes), and propagates quickly through the gossip network. These messages enable most users to learn who is the highest priority proposer, and thus quickly discard other proposed blocks.

Waiting for block proposals. Each user must wait a cer- tain amount of time to receive block proposals via the gossip protocol. Choosing this time interval does not impact Algo- rand’s safety guarantees but is important for performance. Waiting a short amount of time will mean no received pro- posals. If the user receives no block proposals, he or she initializes BA⋆ with the empty block, and if many users do so, Algorand will reach consensus on an empty block. On the other hand, waiting too long will receive all block proposals but also unnecessarily increase the confirmation latency.

To determine the appropriate amount of time to wait for block proposals, we consider the plausible scenarios that a user might find themselves in. When a user starts waiting for block proposals for roundr, they may be one of the first users to reach consensus in round r −1. Since that user completed round r −1, sufficiently many users sent a message for the last step of BA⋆ in that round, and therefore, most of the network is at most one step behind this user. Thus, the user must somehow wait for others to finish the last step of BA⋆ from round r − 1. At this point, some proposer in round r that happens to have the highest priority will gossip their priority and proof message, and the user must somehow wait to receive that message. Then, the user can simply wait until they receive the block corresponding to the highest priority proof (with a timeout λ block , on the order of a minute, after which the user will fall back to the empty block).

It is impossible for a user to wait exactly the correct amount for the first two steps of the above scenario. Thus, Algorand estimates these quantities (λ stepvar , the variance in how long it takes different users to finish the last step of BA⋆, and λ priority , the time taken to gossip the priority and proof message), and waits for λ stepvar +λ priority time to identify the highest priority. §10 experimentally shows that these parameters are, conservatively, 5 seconds each. As mentioned above, Algorand would remain safe even if these estimates were inaccurate.

Malicious proposers. Even if some block proposers are malicious, the worst-case scenario is that they trick different Algorand users into initializing BA⋆ with different blocks. This could in turn cause Algorand to reach consensus on an empty block, and possibly take additional steps in doing so. However, it turns out that even this scenario is relatively unlikely. In particular, if the adversary is not the highest pri- ority proposer in a round, then the highest priority proposer will gossip a consistent version of their block to all users. If the adversary is the highest priority proposer in a round, they can propose the empty block, and thus prevent any real transactions from being confirmed. However, this happens with probability of at most 1−h, by Algorand’s assumption that at least h > 2/3 of the weighted user are honest.