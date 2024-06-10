# lecture 5

Created: February 15, 2024 2:56 PM

# routing protocols

- the goal is to determine “good” paths (equivalently, **routes**), from sending hosts to receiving host, through network of routers
    - **path**: sequence of routers packets will traverse in going from given initial source host to given final destination host

## graph abstraction: network

![Screenshot 2024-02-15 at 2.57.57 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_2.57.57_PM.png)

## graph abstraction: costs

![Screenshot 2024-02-15 at 2.58.26 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_2.58.26_PM.png)

> **key question**: what is the least-cost path between u and z?

**routing algorithm**: algorithm that finds that least cost path
> 

## routing algorithm classification

| global  | all routers have complete topology, link cost information

“link state” algorithms |
| --- | --- |
| decentralized | router knows physically-connected neighbors, link costs to neighbors

iterative process of computation, exchange of info with neighbors

“distance vector” algorithms |
| static | routes change slowly over time |
| dynamic | routes change more quickly
• periodic update
• in response to link cost changes |

# link state

- **dijkstra’s algorithm**
    - net topology, link costs known to all nodes
        - accomplished via “link state broadcast”
        - all nodes have same info
    - computes least cost paths from one node (source) to all other nodes and gives forwarding table for that node
    - **iterative**: after k iterations, know least cost path to k destination

- notation
    - **c(x,y)**: link cost from node x to y; = ∞ if not direct neighbors
    - **D(v)**: current value of cost of path from source to destination
    - **p(v)**: predecessor node along path from source to v
    - **N'**: set of nodes whose least cost path definitively known

## dijsktra’s algorithm

```
initialization:
	N' = {u}
	for all nodes v
		if v adjacent to u
			then D(v) = c(u,v)
		else D(v) = ∞
**Loop**
	find w not in N' such that D(w) is a minimum
	add w to N'
	update D(v) for all v adjacent to w and not in N' :
		D(v) = min( D(v), D(w) + c(w,v) )
/* new cost to v is either old cost to v or known shortest path cost to w plus cost from w to v */
**until all nodes in N'**
```

- algorithm complexity: n nodes
    - each iteration needs to check all nodes, w, not in N’

### example

![Screenshot 2024-02-15 at 3.14.50 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.14.50_PM.png)

![Screenshot 2024-02-15 at 3.15.03 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.15.03_PM.png)

# distance vector

- bellman-ford equation (dynamic programming)
    - similar to dijsktra’s algorithm, however bellman-ford can check for negative edge weights, meaning that it is also capable of detecting negative edge cycles

![Screenshot 2024-02-15 at 3.15.53 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.15.53_PM.png)

### example

![Screenshot 2024-02-15 at 3.16.10 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.16.10_PM.png)

- in forwarding table, the next hop in shortest path is set to **node achieving minimum**
- example for bellman ford
    
    ![Screenshot 2024-03-26 at 10.25.00 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.25.00_PM.png)
    
    - let’s suppose we have a graph and we want to find out whether there exists a negative cycle or not using BF
    
    ![Screenshot 2024-03-26 at 10.27.47 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.27.47_PM.png)
    
    - initialize the distance array to store the shortest distance for each vertex from the source vertex— initially distance of source will be 0 and distance of other vertices will be infinity
    
    ![Screenshot 2024-03-26 at 10.28.37 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.28.37_PM.png)
    
    - current distance of B > distance of A + weight of A to B (e.g. infinity > 0 + 5) so dist[B] = 5
    
    ![Screenshot 2024-03-26 at 10.29.33 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.29.33_PM.png)
    
    - current distance of D > distance of B + weight of B to D (e.g. infinity > 5 + 2) so dist[D] = 7
    - current distance of C > distance of B + weight of B to C (e.g. infinity > 5 + 1) so dist[C] = 6
    
    ![Screenshot 2024-03-26 at 10.32.39 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.32.39_PM.png)
    
    - current distance of F > distance of D + weight of D to F (e.g. infinity > 7 + 2) so dist[F] = 9
    - current distance of E > distance of C + weight of C to E (e.g. infinity > 6 + 1) so dist[E] = 7
    - then keep going !

## algorithm

- Dx(y) = estimate of least cost from x to y
    - x maintains distance vector $Dx = [D_x(y): y є N ]$
- **node x knows cost to each neighbor v**: c(x,v) and maintains its neighbors’ distance vectors — for each neighbor v, x maintains $Dv = [D_v(y): y є N ]$
- from time-to-time, each node sends its own distance vector estimate to neighbors so when x receives new DV estimate from neighbor, it updates its own DV using B-F equation: $D_x(y) ← min_v{c(x,v) + D_v(y)}$ for each node $y ∊ N$
- under minor, natural conditions, the estimate $D_x (y)$ converges to the actual least cost $d_x(y)$

> a router transmits its distance vector to each of its neighbors in a routing packet

each router receives and saves the most recently received distance vector from each of its neighbors

a router recalculates its distance vector when:
   - it receives a DV from a neighbor containing different information
   - it discovers that a link to a neighbor has gone down
> 

- example for distance vector
    - consider 3-routers X, Y, and Z
    
    ![Screenshot 2024-03-26 at 10.41.11 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.41.11_PM.png)
    
    - consider router X— X shares its routing table to its neighbors who share their routing table to X
        - the distance is calculated with the BF equation: Dx(y) = min { C(x,v) + Dv(y) } for each node
    
    ![Screenshot 2024-03-26 at 10.43.18 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-03-26_at_10.43.18_PM.png)
    

## iterative, asynchronous

- each local iteration caused by local link cost change and DV update message from neighbor

![Screenshot 2024-02-15 at 3.19.39 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.19.39_PM.png)

## distributed

- each node notifies neighbors only when its DV changes
    - neighbors then notify their neighbors if necessary

![Screenshot 2024-02-15 at 3.20.07 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.20.07_PM.png)

![Screenshot 2024-02-15 at 3.20.15 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.20.15_PM.png)

## link cost changes

- node detects local link cost change, updates routing info, recalculates distance vector if DV changes, and notifies neighbors

![Screenshot 2024-02-15 at 3.21.04 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.21.04_PM.png)

![Screenshot 2024-02-15 at 3.22.26 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.22.26_PM.png)

- **poisoned reverse**: if Z routes through Y to get to X, Z tells Y its (Z’s) distance to X is infinite (so Y won’t route to X via Z)

## LS vs DV algorithms

|  | LS | DV |
| --- | --- | --- |
| message complexity | with n nodes, E links, O(nE) msgs sent | exchange msgs between neighbors only |
| speed of convergence | requires O(n2) computations and may have oscillations | convergence time varies

may have routing loops

count-to-infinity problem |
| robustness | node can advertise incorrect link cost

each node computes only its own table | node can advertise incorrect path cost

each node’s routing table can be used by others

error propagates thru network |

> DV is a dynamic routing algorithm in which each router computes a distance between itself and each possible destination and shares its knowledge about the whole network to its neighbors so it can update the table based on its neighbors
> 

> LS is a dynamic routing algorithm in which each router shares knowledge of its neighbors with every router in the network— a router sends its information about its neighbors only to all the routers through flooding
> 

## making routing scalable

- scale with billions of destinations
    - can’t store all destinations in routing tables
    - routing table exchange would swamp links

- administrative autonomy
    - internet = network of networks
    - each network admin may want to control routing in its own network

# aggregate routes (autonomous systems)

- an autonomous system is a large network or a group of networks that have a unified routing policy

- **intra-AS routing**
    - also known as interior gateway protocols (IGP)
    - routing among hosts, routers in same AS (“network”)
    - all routers in AS must run same intra-domain protocol
    - routers in different AS can run different intra-domain routing protocol
    - **gateway router**: at “edge” of its own AS, has link(s) to router(s) in other AS’es

- **inter-AS routing**
    - routing among AS’es
    - gateways perform inter-domain routing (as well as intra-domain routing)

## interconnected ASes

- forwarding table configured by both intra and inter-AS routing algorithm

- intra-AS routing algorithm determines entries for destinations within AS

- inter-AS & intra-AS algorithms determine entries for external destinations

## inter-AS tasks

- suppose router in AS1 receives datagram destined outside of AS1 — router should forward packet to gateway router, but which one?
- AS1 must:
    1. learn which destinations are reachable through AS2, which through AS3
    2. propagate this reachability info to all routers in AS1

### example: setting forwarding table in router

- suppose AS1 learns (via inter-AS protocol) that subnet x is reachable via AS3 (gateway 1c), but not via AS2
    - inter-AS protocol propagates reachability info to all internal routers
- router 1d determines from intra-AS routing info that its interface I is on the least cost path to 1c
    - installs forwarding table entry (x,I)

![Screenshot 2024-02-15 at 3.29.31 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.29.31_PM.png)

### example: choosing among multiple ASes

- now suppose AS1 learns from inter-AS protocol that subnet x is reachable from AS3 and from AS2
- to configure forwarding table, router 1d must determine which gateway it should forward packets towards for dest x

![Screenshot 2024-02-15 at 3.30.23 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.30.23_PM.png)

- **hot potato routing**: send packet towards closest of two routers

![Screenshot 2024-02-15 at 3.30.47 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.30.47_PM.png)

## RIP (routing information protocol)

- included in BSD-UNIX distribution in 1982
- distance vector algorithm
    - **distance metric**: # hops (max = 15 hops), each link has cost 1
    - DVs exchanged with neighbors every 30 sec in response message (aka **advertisement**)
    - each advertisement lists of up to 25 destination subnets (in IP addressing sense)
- routing tables managed by application-level process called **route-d (daemon)**
    - advertisements periodically sent in UDP packets

![Screenshot 2024-02-15 at 3.32.43 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.32.43_PM.png)

## OSPF (open shortest path first)

- “open”: publicly available
- uses **link-state algorithm**
    - link state packet dissemination
    - topology map at each node
    - route computation using dijkstra’s algorithm
- router floods OSPF link-state advertisements (for each attached link) to all other routers in entire AS
    - **OSPF messages directly over IP (rather than TCP or UDP)**

### OSPF’s advanced features

| security | all OSPF messages authenticated (to prevent malicious intrusion) |
| --- | --- |
| multiple same-cost paths allowed | for each link, multiple cost metrics for different services (e.g., satellite link cost set “low” for best effort service; high for real time service) |
| integrated uni and multicast support | multicast OSPF (MOSPF) uses same topology data base as OSPF |
| hierarchical | OSPF in large domains |

![Screenshot 2024-02-15 at 3.34.48 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.34.48_PM.png)

## hierarchical OSPF

- **two-level hierarchy**: local area, backbone
    - link-state advertisements only in local area
    - each nodes has detailed area topology; only know direction (shortest path) to nets in other areas

- **area border routers**: “summarize” distances to nets in own area, advertise summaries to other area border routers

- **backbone routers**: run OSPF routing limited to backbone
    - connect to other AS’es

# internet inter-AS routing: BGP

- **BGP (border gateway protocol)**: the de facto inter-domain routing protocol
    - “glue that holds the internet together”
- BGP provides each AS a means to:
    - **eBGP**: obtain subnet reachability information from neighboring ASes
    - **iBGP**: propagate reachability information to all AS-internal routers
        - determine “good” routes to other networks **based on reachability information and policy**

![Screenshot 2024-02-15 at 3.36.44 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.36.44_PM.png)

## BGP basic

- **BGP session**: a “path vector” protocol that includes two BGP routers (“peers”) to exchange BGP messages over semi-permanent TCP connection— advertising paths to different destination network prefixes
- **policy-based routing**:
    - gateway router receives route advertisements and uses important policy to accept/decline path (e.g., never route through AS Y)
    - AS policy also determines whether to advertise path to other neighboring ASes

## BGP path advertisement

![Screenshot 2024-02-15 at 3.38.55 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.38.55_PM.png)

- when AS3 gateway router 3a advertises path AS3,X to AS2 gateway router 2c, AS3 promises to AS2 it will forward datagrams towards X

![Screenshot 2024-02-15 at 3.39.42 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.39.42_PM.png)

- AS2 router 2c receives path advertisement AS3,X (via eBGP) from AS3 router 3a
- based on AS2 policy, AS2 router 2c accepts path AS3,X, propagates (via iBGP) to all AS2 routers
- based on AS2 policy, AS2 router 2a advertises (via eBGP) path AS2, AS3, X to AS1 router 1c

![Screenshot 2024-02-15 at 3.40.22 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.40.22_PM.png)

- gateway router may learn about multiple paths to destination:
    - AS1 gateway router 1c learns path AS2,AS3,X from 2a
    - AS1 gateway router 1c learns path AS3,X from 3a
    - based on AS1 policy, AS1 gateway router 1c chooses path AS3,X, and advertises path AS3,X within AS1 via iBGP

### forwarding table entries

- how does router set forwarding table entry to distant prefix?

![Screenshot 2024-02-15 at 3.41.05 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.41.05_PM.png)

## BGP route selection

- router may learn about more than one route to destination AS, selects route based on:
    1. shortest AS path
    2. closest next-hop router: hot potato routing
    3. local preference value attribute: policy decision
    4. additional criteria

## hot potato routing

![Screenshot 2024-02-15 at 3.42.21 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.42.21_PM.png)

- 2d learns (via iBGP) it can route to X via 2a or 2c
- **hot potato routing**: choose local gateway that has least intra-domain cost (e.g., 2d chooses 2a, even though more AS hops to X)

## BGP policy routing

![Screenshot 2024-02-15 at 3.42.55 PM.png](lecture%205%207b4f14b886064011b73eec5aa5d48bc1/Screenshot_2024-02-15_at_3.42.55_PM.png)

- suppose an ISP only wants to route traffic to/from its customer networks (does not want to carry transit traffic for other ISPs)
    - A,B,C are provider networks
    - X,W,Y are customers (of provider networks)
    - X is dual-homed: X attaches to two networks
    - policy routing X does not want to route from B to C via X so X will not advertise to B a route to C

### why different intra, inter-AS routing?

| policy | inter-AS: admin wants to control over how its traffic is routed, who routes through its net

intra-AS: single admin, so no policy decisions are needed |
| --- | --- |
| scale | hierarchical routing saves table size, reduces update traffic |
| performance | intra-AS: can focus on performance

inter-AS: policy may dominate over performance |