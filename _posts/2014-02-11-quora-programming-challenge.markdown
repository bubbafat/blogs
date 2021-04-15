---
author: admin
comments: false
date: 2014-02-11 22:00:48+00:00
layout: post
link: https://roberthorvick.com/2014/02/11/quora-programming-challenge/
slug: quora-programming-challenge
title: Alma (Quora) Heating Duct challenge ...
wordpress_id: 326
---

Back sometime around June 2009, Alma Networks (now Quora) had an interesting [programming challenge](https://web.archive.org/web/20090627214413/http://www.alma-networks.com/challenges/) - specifically the data center cooling challenge.  I had some time to kill so I decided to tackle it.

I forget the details, but I said something about the challenge (probably on Twitter) and Charlie Cheever reached out to me asking about my answer.  Since I was just killing time, I didn't formally submit my result to Charlie but I did send him my notes and solution (hacky as they were).  Out of respect for their hiring process I did not share my answer.

Fast forward to 2014 ... I was setting up a new backup process for our house and came across the code.  Since Alma is now Quora, years have passed and Quora has a whole [new set of challenges](https://www.quora.com/about/challenges), I have no problem posting what I shared - this is a copy/paste of the email.

**tl;dr** How I solved a programming challenge and took the solution from 2 hours (brute force) to 2 seconds (.00026% of the brute force time).

  



* * *


  


My notes on the approach I took to optimizing the air duct problem.  The code is attached.  My answer to the reference problem was 301716.

Obviously I can't validate that manually but I get the same answer using each of the optimization approaches and all of my manually validated test cases are accurate ... so I feel good about it.
I sincerely hope I don't have a glaring bug that makes me look like an ass!

Robert.



## Graph Validation Rules



Run once after the graph is loaded and before enumeration.  The goal here is to determine that there is no reason to even start. 

**Rule 1: Every open node need have at least one adjacent open node to be a valid graph. **

_Example: in the following graph there is no way to get to the center node so there is no reason to walk any paths._
 
`
2 0 0 0 0
0 1 1 1 0
0 1 0 1 0
0 1 1 1 0
0 0 0 0 3
`

**Rule 2: If there is an open node (A) which has only one adjacent open node (B) then node A must be either the start or end node for the graph to be valid.**

_Example: Every path must be able to get to the middle node and then continue to the end node - that is obviously impossible._

` 
2 0 0 0 0
0 1 1 1 0
0 1 0 0 0
0 1 1 1 0
0 0 0 0 3
`

**Path Validation Rules**
 
Once we have a graph with a potential path I start the brute force iteration with the goal of not walking as many paths as possible (i.e. _instead of trying to find the valid paths faster I try to find the invalid paths faster_).
 
Each of these rules is applied on every move.  The rules are run after the current node is marked as visited but before the next node is visited.
 
**Rule 3: If there is exactly one adjacent node with exactly one open adjacent node then that is the only node you need to visit.**

_Example: The start node has three options - west, south and east.  However only could produce a valid path - west.  If you choose east or south there is no way to get to the 0,0 node and still get to the end node [see Rule 2] (note: indexing starts at 0,0 and works down and right to width,height)._

` 
0 2 0 0
0 0 0 0
0 0 0 3
` 

**Rule 4: If there is more than one adjacent node with exactly one open adjacent node then there is no valid mode from the current node.**

_Example: No matter what choice you make you cannot create a valid path.  Every option results in at least one blocked node._
 
`
0 2 0
0 0 0
0 0 3
` 
 
**Rule 5 If the start node is an edge node (i.e. it bounds the graph edge) and the current node (A) is not an edge and the next move puts us on an edge node (B) and there exists more than one adjacent open node to node B (e.g. north and south are open, east was where we came from and west is a wall) then we can stop this path because one of the paths is now blocked and cannot form a valid path.
**

This rule caused me to adjust the loading logic to say that if the start node were not an edge but the end node was - flip the meaning of the start and end nodes to allow this optimization to be used.  If both the start and end nodes are not edges then this rule cannot be used since there is no certainty that going to a wall creates a block.
 
That's a mouthful.  Basically what it says is that if you cut off any open nodes from each other there is no way to create a valid path between them.
 
_Example: Here we're starting at the corner and dividing the graph in half.  The nodes on the north are cutoff from the nodes on the south - therefore we can't create a path between them and there is no reason to go on.  The nice part is that we don't even care where the end node is.  All we care is that the current node has two open neighbors (where we came from is visited and the edge is non-vistable so we don't need to do north/south or east/west checks - just "open neighbor count")._
 
`
2 0 0 0 0 0 0
| 
0 0 0 0 0 0 0
|
0-0-0-0-0-0-0
0 0 0 0 0 0 0
0 0 0 0 0 0 3
`
 
_Note: this approach cannot optimize the following because we hit a wall, not an edge:_

` 
2 0 0 0 0 0 0
| 
0 0 0 0 0 0 0
|
0-0-0-0-0-0 1
0 0 0 0 0 0 0
0 0 0 0 0 0 3
` 

Adding a behavior to walk the wall/edge/visited node path to search for a cycle could be used here but I don't think the trade-off is worth it without radically changing the algorithm to remove the distinction between a wall ('1' node) and an edge (outer borders of the graph) and even then the number of times you'd run the search would probably negate any benefit except for special cases (i.e. huge graphs where there are known to be many open nodes inside and out of the cutoff.)
 
_Note: this approach cannot optimize the following because neither start or end is an edge node:
_
 
`
0 0 0 0 0 0 0
0 2-0-0-0-0-0
0 0 0 0 0 0 0
0 0 0 0 0 3 0
0 0 0 0 0 0 0
`
 
Hitting a wall here is not a problem - we can go north, west and then still find a complete path. 
Solution:  Any time you hit the end node you are only done if every other node is visited.

I kept graph-wide counter of open nodes.  Every time I visit an open node I mark the node as visited and decrement counter.  Everytime I unvisit a node I mark the node as open and increment the counter.  Hitting the end node is only valid when doing so makes the open node counter 0.

Doing this means I don't need to keep any path data (reducing workload and improving locality since the core structures stay hot).
 
_I think there is a Rule 7 that is something like "if there exists any node that does not have a path to the end node then the graph is a bust." but I believe that the over-head of maintaining that information may not be worth it (yet). _
  
Code and timing notes ... 

Brute force time on the reference graph: 7561 seconds (over 2 hours)
 
Time with only Rule 5 applied: 201 seconds   (2% of brute force)
 
Time with only Rules 3 and 4 applied: 4 seconds (.00055% of brute force)
 
Time with Rule 3, 4 and 5 applied:  2 seconds (.00026% of brute force)
 
The answer produced using each approach was 301716.
 
[Main.cpp](https://github.com/bubbafat/alma/blob/master/main.cpp) - within main there are two paths.  If the word "tests" is passed in argv[1] then the builtin tests are run (the test input files need to be in the current directory).  If argv[1] is not "tests" then the graph is read from stdin.  Additionally if "tests" is passed after the graph is read from the input file an additional "expected" count is read that is used to validate the result.
 
[Graph.h](https://github.com/bubbafat/alma/blob/master/graph.h) [Graph.cpp](https://github.com/bubbafat/alma/blob/master/graph.cpp) - this is the graph loading and validation logic.  graph::load is where the input stream is read (which is a lame design - the parser should not live in the graph and it's fairly optimistic).  Once loaded the graph is validated (is_valid()) and any start/end node swapping is done and we determine if Rule 5 can be applied (detect_divider_lines = ...).
 
[node.h](https://github.com/bubbafat/alma/blob/master/node.h) [node.cpp](https://github.com/bubbafat/alma/blob/master/node.cpp) - this is the meat and potatos.  node::visit() is where the bulk of the logic happens.  First we check if we're at the end and apply the solution validation rule.  Next we mark the node visited and decrement the graph open node count.  After that we setup for Rule 3-4. 
 
The code is a little hackish because I favored not repeating the calls to get the neighbor counts (which ends up being a very hot function) - doing this meant carrying some state around.  The "go*" booleans seem redundant but the perform of using a boolean versus doing a check against the *_open_count variables was surprising.  This is probably a compiler specific optimization.
 
If we can apply rule 3 [if(adjacentNodesWithOnePath == 1)] or 4 [open else block with no content] we do.  Otherwise we take the brute force approach [if(adjacentNodesWithOnePath == 0)].  Once a destination is choosen we call "go" - go does the null check and does a check for rule 5.
 
Both visit and go have a check for "use_optimizations" - I left this in so swapping back to brute force would be possible when adding new tests.  Everything else is just sugar.

[Test_*.txt - the tests](https://github.com/bubbafat/alma/tree/master/tests).  Most are referenced in run_prebuilt_tests (main.cpp) but a few aren't (the parser tests that cause the program to abort with an error message).
