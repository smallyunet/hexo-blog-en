---
title: Recent Work in the Past Six Months
tags: Work
date: 2024-02-17 10:49:03
draft_date: 2024-02-17 09:42:15
---

The main work over the past six months has focused on three main tasks.

#### Building the Abstract Account (ERC-4337) Project

An AA account is essentially a smart contract that implements features such as zero fees and social recovery through additional bundler and paymaster components. ERC-4337 defines a set of standards, including the interfaces that the contract must support, how the bundler interacts with the contract, and the role of the paymaster. Currently, there are roughly three to five different implementations for each component, with some variations in details, but all follow the ERC-4337 standard.

Last year, the concept of abstract accounts gained some popularity, possibly because Vitalik mentioned Soulbound. Project teams used AA accounts as a solution for Soulbound, creating some hype. Innovations like ERC-4337, which define standards rather than change the underlying technology, generally don't have long-lasting impacts. It's a series of standardized usage methods. While it might provide short-term solutions, it's not a bad thing.

#### Using Op Stack

Since Optimism open-sourced the entire Op Stack toolkit, launching an Optimistic Layer 2 has become easier. This led to the trend of one-click chain launches last year, with Base, opBNB, and others going live in a short period. Although Optimism's Fault Proof is not yet perfect, even a slightly centralized project can be stable enough. The Op Stack can fully support launching your own network.

We ran AA accounts on a Layer 2 network, which supported a small gaming event. Despite some challenges along the way, we gained valuable experience. Diving deeply into these two types of projects is impossible within a limited timeframe, as each direction can be a specialized field. Currently, the status is barely usable.

However, have you noticed that concepts like AA accounts and one-click chain launches are short-lived? They were hot last year, but no one talks about them this year. This year, the focus has shifted to Bitcoin's Layer 2, inscriptions, ERC-404, etc. Hot topics are always changing.

#### Building an Ethereum PoS Network

Since The Merge, building the Ethereum network has become more complex. Although it saves the computational power consumption of mining, it requires the introduction of a complex validator governance mechanism to ensure orderly block production. PoS inevitably involves issues like becoming a validator and determining valid validators. Previously, nodes were divided into two types, execution layer and consensus layer. PoS solves the ungraceful block production issue of PoW, but at the cost of a complex set of rules.

Ethereum is currently the most diverse ecosystem chain, with five or six clients each for the execution layer and consensus layer, all implementing standard interfaces but with different experiences, especially in performance. A lot of time was spent testing each client.

Ethereum's greatest asset is the EVM, not PoS. EVM compatibility has become a selling point for various chains. PoS was not invented by Ethereum; Cardano used PoS from the start and retained the UTXO model. Occasionally, people say Bitcoin and Cardano are the future. Compared to Ethereum, Cardano's consensus mechanism is more like a Bitcoin alternative rather than Ethereum.

Bitcoin and Ethereum belong to two camps. Bitcoin focuses on cryptocurrency, while Ethereum explores applications beyond currency. Under each camp, many altcoins are active.
