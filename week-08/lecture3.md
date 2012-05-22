# CS For Hackers - Lecture 8.3
## Distributed systems and the CAP Theorem

When we're done, you should understand:

- Fallicies of distributed computing
- Distributed Hash Tables (the Chord algorithm)

----

# But first, a video.

<p><a href="http://www.xtranormal.com/watch/6995033/mongo-db-is-web-scale" target="_blank" style="font-size: 14px;font-weight:bold;">Mongo DB Is Web Scale</a><br />by: <a href="http://www.xtranormal.com/profile/3201665" style="" target="_blank">gar1t</a></p><iframe id="xtranormal_Mongo DB Is Web Scale" name="xtranormal_Mongo DB Is Web Scale" style="width:480px;height:299px;" src="http://www.xtranormal.com/xtraplayr/6995033/mongo-db-is-web-scale" marginwidth="0" marginheight="0" border="0" frameborder="0" scrolling="auto"></iframe>

<http://www.xtranormal.com/watch/6995033/mongo-db-is-web-scale>

## Presenter Notes

- "Web scale" systems are by nature distributed systems
- Largely, the problem of doing things web scale is getting these systems right
- Remember Moore's Law? Building things web scale is our challenge.

----

# Fallacies of Distributed Computing

Peter Deutsch and James Gosling (Sun):

1. The network is reliable.
2. Latency is zero.
3. Bandwidth is infinite.
4. The network is secure.
5. Topology doesn't change.
6. There is one administrator.
7. Transport cost is zero.
8. The network is homogeneous.

I would add at least:

9. The disk is reliable
10. The OS is reliable
11. The program is correct

----

# Distributed Hash Table (DHT)

- A real web-scale sytem! 
- A hash table that stores O(n) data (n = nodes).
- P2P (though maybe used to power a server in client/server model)

DHTs you may have heard of:

- Amazon Dynamo
- Apache Cassandra
- Project Voldemort
- BitTorrent 
- Coral CDN

----

# Exercise: Let's build a DHT

- How does a hash table work?
- How does it work with many nodes?
- What happens when a new node joins?
- What happens when a node fails?

## Presenter Notes

- Many nodes Ideas: devote bit, mod
- What fallacies do you run into? Topology, network reliable, homogeneous
- How does key distribution work when you add a node?
- Note the above is a common sharding problem

----

# Chord: simple and elegant DHT

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/chord-abstract.png)

<http://pdos.csail.mit.edu/chord/>

- Great paper to read
- Many practical DHTs are built on these principles
- Contians several really good ideas about distributed systems

## Presenter notes

- This started it all for me. Made me like DHTs before they were popular.

----

# Chord big idea #1

- Idea: Hash data and machines the same way (predates chord)
- This is called **consistent hashing**
- Each machine is responsible for keys it is the **successor** node

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/successor.png)

----

# Aside: Hashing in practice

Hashing is not difficult, although coming up with the algorithm may be:

```python
    # Hash something
    import hashlib
    hashlib.sha1("Hello, world.").hexdigest()
    '2ae01472317d1935a84797ec1983ae243fc6aa28'

    # Take only 64 bits worth
    int(hashlib.sha1("127.0.0.1:1337").hexdigest()[:16], 16)
    13773325924649937414L

    # Hash Shakespeare's Much Ado About Nothing
    import urllib2
    much = 'http://www.gutenberg.org/cache/epub/1519/pg1519.txt'
    ado = urllib2.urlopen(much).read()
    hashlib.sha1(ado).hexdigest()
    '45e282bb69f1b8b6223149bd1bdbe669325c9155'

    # Take only 8 bits worth
    int(hashlib.sha1(ado).hexdigest()[:2], 16)
    69
```

----

# Finding a key

To find the node where `key` resides:

```python
    def find_successor(self, key):
        if self.key < key < self.successor.key:
            return successor
        else:
            return self.successor.find_successor(key)
```

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/simple-successor.png)

- How fast is this? Can we find it faster?

----

# Chord big idea #2

- The exponential finger table
- Store the key for nodes exponentially further away from you

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/finger-map.png)

- How much storage space will the finger table take?
- What does the finger table remind you of?

----

# Faster lookups

```python
    def find_successor(self, key):
        if self.key < key <= self.successor.key:
            return successor
        else:
            jump = closest_preceeding_node(key)
            return jump.find_successor(key)

    def closest_preceeding_node(self, key):
        for finger in reversed(self.finger_table):
            if finger.key < key:
                return finger
        return self
```

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/finger-lookup.png)

----

# Ring maintenance

```python
    def __init__(self):
        self.predecessor, self.successor = None, self

    def join(self, existing_node):
        self.predecessor = None
        self.successor = existing_node.find_successor(self.key)
        self.copy_keys(existing_node)

    def stabilize(self): # on timer
        candidate = successor.predecessor
        if self.key < candidate.key < self.successor:
            self.successor = candidate
        self.successor.notify(self)

    def notify(self, other):
        if self.predecessor is None or self.predecessor.key < other.key < self.key:
            self.predecessor = other

    def predecessor_failed(self): # on timer
        if self.predecessor.check_failed():
            self.predecessor = None

    def fix_fingers(self): # on timer
        self.next = (self.next + 1) % len(self.finger_table)
        next_index = self.key + 2^(self.next - 1)
        self.finger_table[self.next] = self.find_successor(next_index)
```

## Presenter notes

- This is all! All the code necessary!

----

# Join example

- Existing ring: N21, N32, K24, K30
- N26.join(N32)

----

# Some questions about Chord

- How big does it have to be to become useful?
- How would you avoid hot spots?

----

# Brewer's CAP theorem

> Consistency, Availability, Partition tolerance. Choose any two. 

- Famously formulated by Eric Brewer (Inktomi) at [PODC](http://www.podc.org/podc2000/) in 2000
- Proved by Gilbert and Lynch in 2002

**Consistency:** Once you update something, it shows as updated. It works, or not at all. (examples: atomicity, transactions)

**Availability:** Always return a result, don't fail.

**Partition tolerance:** If your servers get separated, things still work. (except for total network failure)

The bigger you are, the more you hit CAP limits.

Example: Two servers, write on one, replicate to the other.

----

# Dealing with CAP limitations

Eventual consistency!

Further reading:

- <http://www.julianbrowne.com/article/viewer/brewers-cap-theorem>
- <http://www.allthingsdistributed.com/2008/12/eventually_consistent.html>
- <http://nathanmarz.com/blog/how-to-beat-the-cap-theorem.html>

----

# Lamport and vector clocks

- How do you backup a distributed system?
- How would you unroll transactions across multiple nodes?

Both these questions involve taking action that is *fixed in time*.

----

# Fin
