---
title: Pebbling Game
tags: Documentation
date: 2023-05-18 16:36:33
draft_date: 2023-05-18 15:33:55
---

This is an online web game: <a href="https://smallyunet.github.io/pebbling-game/" target="_blank">Pebbling Game</a>. You can view this embedded web page:

<div>
<iframe src="https://smallyunet.github.io/pebbling-game/" width="900px" height="580px" frameborder="0" scrolling="yes" style="border: 5px double #e4e4e4;"> </iframe>
</div>

To intuitively demonstrate the rules of the Pebbling Game, GPT-4 created this online game page after dozens of adjustments.

The rules of the game are:

1. Click on the circle of a node to place a pebble in it.
2. A node can only have a pebble placed in it if all nodes pointing to it already have pebbles.
3. The goal of the game is to place a pebble in node 0.
4. Pebbles can be removed from any node at any time.

If you directly click on node 0, you will see two red flashing circles indicating that nodes 1 and 2 have not yet had pebbles placed in them, so node 0 cannot have a pebble placed in it.

Node 7 has no incoming nodes, so a pebble can be placed directly in it. Clicking on node 7, you will see a solid black circle appear in the node. At this point, if you try to place a pebble in node 3, it will indicate failure because node 6 is still empty. Nodes 6 and 7 are the incoming nodes for node 3.

So, with these game rules, the question is: What is the minimum number of pebbles needed?

If there are enough pebbles, there are a total of 10 nodes in this diagram, and if you have 10 pebbles, you can simply fill the nodes in order without needing to remove any pebbles.

If pebbles are limited, seeking the solution with the least number of pebbles, this diagram should require at least 5 pebbles.

The characteristic of the Pebbling Game is that there is always a minimum value. If the number of pebbles is less than this value, the game cannot be completed because the final node depends on lower-level nodes, which in turn depend on even lower-level nodes. If a pebble is removed from an intermediate node, you have to start placing pebbles from the lowest level again.

The Pebbling Game has enlightening significance for the data structure of Hard-to-pebble graphs. Understanding the rules of the game helps in understanding how blockchain proves the size of disk space.

### Proof of Space

Hard-to-pebble graphs are a type of DAG combined with a Merkle tree. The characteristic is that a certain amount of storage space is required to complete the computation of the top node. Just like in the game where you cannot complete it without enough pebbles, you cannot complete the challenge without enough storage space.

Due to the variety of graphs, there is no universal optimal solution for the number of pebbles required. It can only be calculated for a specific type of graph to determine the space complexity.

In blockchain scenarios, the requirement is to occupy large space and verify quickly. The graph structure of the Stack Expander Graph is widely used in Proof of Space. During the verification phase, you only need to verify certain nodes in the graph according to the characteristics of the Merkle tree to confirm the integrity of the graph. At the same time, you can estimate the amount of disk space occupied based on the depth of the graph.

If you need to verify both the size of the space and the duration of space occupation, you can add a time proof on top of the space proof. For example, Chia uses a Delay Verifiable Function, first verifying the space proof, then after some time, using the VDF to verify that enough time has indeed passed, and then verifying the space proof again, achieving the effect of Proof of Space-Time.
