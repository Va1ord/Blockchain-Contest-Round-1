# Blockchain-Contest-Round-1

Deadline: 23:59, February 4th (Dubai time) 
Tasks: Blockchain Validation (https://contest.com/docs/BlockValidationChallenge) (C++ code optimization)

ContestBot (https://t.me/contestbot) is now ready to accept submissions for Blockchain Contest Round 1. (https://t.me/contest/393)

for “TON Block Validation Challenge” 
an archive containing the following: 
– solution.patch – a patch to the original branch.
– README.md – a description of the implemented optimizations and their effectiveness. If you tried some optimizations that didn't work, kindly provide details regarding them as well.

For this challenge, your submissions will be tested in the following environment:
OS: ubuntu-22.04
Compiler: clang-14
Hardware: AX-42 Hetzner

for “TON Trustless Bridge Challenge”
an archive containing the following:
1. Code of Lite-client smart-contract and script for generation messages for that contract with build instructions.
2. Code of Transaction-checker smart-contract and script for generation messages for that contract with build instructions.
3. Addresses of smart-contracts deployed in Testnet and Fastnet respectively, with the tx_hash of transactions that demonstrate both successful and unsuccessful checks of blocks and transactions.

So, I would like to suggest using Dijkstra's (Дейкстры) algorithm https://ru.wikipedia.org/wiki/Алгоритм_Дейкстры to optimize the code.
This is the most optimal algorithm for finding the shortest path in a weighted graph.
