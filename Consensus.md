# A Review of Consensus Algorithms


## BFT

Byzantine Fault Tolerant systems use a consensus algorithm
that is not only tolerant to faulty nodes that may arbitrarily
fail, or faulty networks that may drop packets. They also
maintain consistency in the presence of "byzantine" nodes that
may send false messages, especially sending different messages
to different recipients.

Leslie Lamport first formalized this issue in his 1982 paper,
[The Byzantine Generals Problem](./consensus/the-byzantine-generals-problem82.pdf), which is the source of
the name BFT and the theoretical basis for this area of
research.

Following up on this paper, came the FLP Impossibility Theorum,
or ["Impossibility of Distributed Consensus with One Faulty Process"](./consensus/flp85.pdf). This argued that with an
**asynchronous** network with arbitrary delays and no timeouts,
it is impossible to securely get consensus in the presence of
one Byzantine process. Since then, almost all BFT research
assumes synchronicity, using an upper-bound on message delay
in order to avoid the FLP impossibility.
(Here is a [nice summary of the paper](http://the-paper-trail.org/blog/a-brief-tour-of-flp-impossibility/))

Ben-Or responded with a paper on ["Complete Asynchronous Agreement Protocols"](./consensus/free-choice83.pdf), where he
demonstrates a solution for asynchronous communication by
introducing an element of non-determinism, by probabalistically
sending a response. This avoids an external scheduler from being
able to control the outcome. It is however slower than other
BFT algorithms, and requires 5f+1 processes, but demonstrates
some nice results. (Again, you can read a
[short summary of the paper](https://brooker.co.za/blog/2014/01/12/ben-or.html))

Beyond these fundamental paper, there were various designs,
but not much research until the blockchain era.

### PBFT

["Practical Byzantine Fault Tolerance"](./consensus/pbft99.pdf)
was the first paper that presented a BFT algorithm that was
reasonable to implement in production systems. This is a
synchronous algorithm, relatively fast, and requires
3f+1 nodes (< one third faulty processes). This is probably
the most complete, production-ready algorithm published before
the blockchain era, and is a model for most of the more
recent algorithms.

There are some [implementations of the PBFT algorithm](https://github.com/luckydonald/pbft),
but mostly seem to be proofs-of-concept, and not ready
for production usage.

### [Tendermint](https://tendermint.com)

After considering the problems related to mining Bitcoin,
Jae Kwon rediscovered much BFT research and realized he could
adapt it to a blockchain setting. The batching of transactions
into blocks allowed nice efficiency gains and this was a
practical setting where BFT would actually be worth the effort
to implement. Jae Kwon the [proposed the use of BFT for blockchains](./consensus/tendermint-kwon14.pdf) in 2014.
Shortly afterwards, he worked with Ethan Buchmann to
[formally specify the protocol](./consensus/tendermint-buchmann16.pdf),
which evolved to be rather similar to
the PBFT paper, but with some important adaptions and
optimizations for this use-case.

There is an [actively developed implementation](https://github.com/tendermint/tendermint/)
in golang that is approaching the 1.0 production release.

### Honeybadger

Honeybadger is the name of [an interesting BFT algorithm](./consensus/honeybadger16.pdf)
from Andrew Miller, also designed for the blockchain.
However, it aims for asynchronous network model
by adding non-determinism into the heart of the consensus
algorithm. In practical terms, it is designed for situations
with poor connectivity, whether due to mobile links or
tunnelling through Tor, and may be very useful in non-standard networks.

There is a [prototype implementation in python](https://github.com/amiller/HoneyBadgerBFT) by the author of the paper,
and it seems the beginning of a
[Rust implementation](https://github.com/rphmeier/honeybadger).
