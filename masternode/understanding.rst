.. meta::
   :description: Explanation of how MarteX masternodes work in theory and practice to support FastSend, AnonSend and governance
   :keywords: martex, masternodes, hosting, linux, payment, fastsend, anonsend, governance, quorum, bls, 

.. _understanding_masternodes:

=========================
Understanding Masternodes
=========================


Masternodes, once unique to the Dash network, are now becoming popular
as the technology is forked into other blockchains. This section of the
documentation describes the principles and mechanisms of masternodes and
the services they provide to the MarteX network specifically.

Simply put, a masternode is a server with a full copy of the MarteX
blockchain, which guarantees a certain minimum level of performance and
functionality to perform certain tasks related to block validation, as
well as AnonSend and FastSend, as the privacy and instant
transaction features in MarteX are called. The masternodes are paid for
this service, using a concept known as Proof of Service. This is in
addition to the Proof of Stake done by miners to secure the blockchain.
Masternodes are also allowed to vote on `governance and funding proposals <./governance_proposal>`_, with each masternode receiving one vote
(yes/no/abstain) on each proposal submitted to the system.

Anyone can run a masternode. The objective is to have enough
decentralization to ensure that no single person controls a significant
fraction of the masternodes. However, to avoid bloating the network with
unnecessary masternodes or encouraging reckless operators, there is one
condition that needs to be fulfilled: proof of ownership of 5000 MarteX.
The coins don't need to be in the masternode, but they need to be kept
in a certain way that is transparent to the entire network. If the owner
moves or spends those coins, the masternode stops working and payment
ceases.

Masternodes are paid by the network for the AnonSend, FastSend and
governance services they provide. 45% of the block reward is paid out to
the masternodes, 45% to miners and 10% to the budget. In practice, half
of the reward from a normal block goes to the miner and half to the
masternode. Then, every 21800 blocks , a
superblock is created that contains the entire 10% payout to the budget
proposal winners. Masternodes are selected for payment in each block
 from a deterministic masternode list,
and moved to the back of the list after payment. As more masternodes are
created, the duration between payments increases. If the collateral
behind a masternode is spent, or if a masternode stops providing
services to the network for more than one hour, it is removed from the
list until normal service resumes. In this way, masternodes are given
incentive to provide efficient and reliable services to the network.

Having so many servers holding a full copy of the blockchain and working
for the coin can be extremely useful. Thanks to the reward system, there
is no risk of not having enough masternodes, and the developers can rely
on them quickly deploying any new decentralized feature they want to
implement. This is where the true strength of MarteX lies - an
incentivized system of thousands of distributed servers working 24x7
means that MarteX can scale more efficiently and deploy services more
quickly than a blockchain run entirely by unpaid volunteers. The more
masternodes, the better and safer the MarteX network.

As of April 2020, the MarteX network has over 112 masternodes.
The block reward is approximately 2,5 MarteX, so the selected masternode receives
1.125 MarteX per payment.
See `this site <https://masternodes.online/currencies/MXT/>`_ to know masternodes ROI, and `this site
<https://mnrank.com/coin/MXT/>`_ for various real-time
statistics on the masternode network.


Masternodes vs. mining
======================

MarteX, like Bitcoin and most other cryptocurrencies, is based on a
decentralized ledger of all transactions, known as a blockchain. This
blockchain is secured through a consensus mechanism; in the case of 
Bitcoin, the consensus mechanism is Proof of Work (PoW).
In MarteXcoin `Miners <~/mining>` attempt to make a consensus, the consensus mechanism of is Proof of Stake `(PoS) <https://en.bitcoinwiki.org/wiki/Proof-of-stake>`_.
The consensus is achieved by requiring users to stake an amount of their tokens
 so as to have a chance of being selected to validate blocks of
 transactions, and get rewarded for doing so.
If all the other people running the software agree that the consensus was made correctly, the block is added to the blockchain and the miner (know as validator) is rewarded.

MarteX works a little differently from Bitcoin because it has a
two-tier network. The second tier is powered by masternodes (Full
Nodes), which enable financial privacy (AnonSend), instant
transactions (FastSend), and the decentralized governance and budget
system. Because this second tier is so important, masternodes are also
rewarded when miners discover new blocks. The breakdown is as follows:
45% of the block reward goes to the miner, 45% goes to masternodes, and
10% is reserved for the budget system (created by superblocks every
month).

The masternode system is referred to as Proof of Service (PoSe), since
the masternodes provide crucial services to the network. In fact, the
entire network is overseen by the masternodes, which have the power to
reject improperly formed blocks from miners. If a miner tried to take
the entire block reward for themselves or tried to run an old version of
the MarteX software, the masternode network would orphan that block, and
it would not be added to the blockchain.

In short, miners power the first tier, which is the basic sending and
receiving of funds and prevention of doublespending. Masternodes power
the second tier, which provide the added features that make MarteX
different from other cryptocurrencies. Masternodes do not mine, and
mining computers cannot serve as masternodes. Additionally, each
masternode is “secured” by 5000 MARTEX. Those MARTEX remain under the sole
control of their owner at all times, and can still be freely spent. The
funds are locked if the masternode is runnig. However, if the funds are moved or
spent, the associated masternode will go offline and stop receiving
rewards.


.. _payment-logic:

Payment logic
=============

Masternode payments in MarteX version v3.0.6.1-a4933cd-dirty are entirely deterministic
and based on a simple list sort algorithm.

1. The full set, which contains all registered masternodes that have not
   spent their collateral funding transactions.
2. The valid set, a subset of the full set which contains all 
   masternodes which are not marked as Proof of Service (PoSe) banned.

Each masternode in the set of valid masternodes, identified by its
registration transaction ID, is associated with the block at which it
was last paid. If it has never received payment or was banned for
failing to meet the PoSe requirements, then the block at which it was
first registered or at which service was restored is used instead. The
list is sorted in ascending order by this block height and ProRegTx hash
(as a tie breaker in case two masternodes were registered in the same
block), and the first entry is selected for payment.


.. _proof-of-service:

Proof of Service
================

Proof of Service (PoSe) is a scoring system used to determine if a
masternode is providing network services in good faith. A number of
metrics are involved in the calculation, so it is not possible to game
the system by causing masternodes to be PoSe banned for failing to
respond to ping requests by e.g. a DDoS attack just prior to payment.
Each failure to provide service results in an increase in the PoSe score
relative to the maximum score, which is equal to the number of
registered masternodes. If the score reaches the number of registered
masternodes, a PoSe ban is enacted and the masternode must be repaired
to ensure it provides reliable service and registered in the list again.

Quorum selection
================
As of MarteX 3.6, quorums
are deterministically formed, contain more masternodes and remain alive
for a longer period of time. While they remain responsible for
FastSend transactions, the locking mechanism has changed to
automatically attempt locks on most network transactions according to
the requirements described `Broadcast <http://>`_. Masternodes are
now also responsible for more network consensus functions, such as
`Chainlocks <http://>`_. Masternode quorums are formed through a
process of distributed key generation.
Failure to participate in DKG will eventually result in a PoSe ban.


Masternode requirements
=======================

- 5000 MarteX: Arguably the hardest part. MarteX can be obtained from
  exchanges such as CreX24, decentralized markets and exchanges.
- A server or VPS running Linux: Most recent guides use Ubuntu 18.04
  LTS. We recommend VPS services such as Vultr and DigitalOcean,
  although any decent provider will do. Generally an instance with low
  to average specifications will do, although performance requirements
  will increase according to updates in MarteXcoin.
- A dedicated IP address: These usually come with the VPS/server.
- A little time and (heart): Masternodes used to require `complex setup </Documentation/masternode>`_,
  but tools such as `MXT-MN </Documentation/masternode/mxt-mn/>`_ now greatly simplify the process.

In addition to the 5000 Dash held in collateral, masternodes also have
minimum hardware requirements. For MarteX versions 3.6 and higher, these
requirements are as follows:

+---------+------------------+------------------+
|         | Minimum          | Recommended      |
+=========+==================+==================+
| CPU     | 1x 1 GHz         | 1x 2 GHz         |
+---------+------------------+------------------+
| RAM     | 1 GB + 1,5GB swap| 4 GB + 2 GB swap |
+---------+------------------+------------------+
| Disk    | 10 GB            | 20 GB            |
+---------+------------------+------------------+
| Network | 4   GB/mth       | 1 TB/mth         |
+---------+------------------+------------------+
