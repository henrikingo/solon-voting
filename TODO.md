TODO - the Solon Backlog, if you will
=====================================

In this file we try to list the various areas where new contributors can find
tasks to work on. Tasks are categorized by topic, and ordered roughly in the 
sequence they need to be implemented.

The dummy backend
-----------------

While the current dummy backend is just a mockup, and the intent is to now focus
on implementing the real thing with the cryptographic functionality from the
Acquisti paper, there are a few things that would make sense to still implement 
in the dummy backend in order to demonstrate a complete data flow.

 * Currently one major Liquid Feedback feature is still missing from the Solon
   dummy backend: Delegation of your vote per unit or per area. (Per issue
   delegation is implemented.)
 * For issues in state=closed, show voting results also in the Solon dummy UI.
   (Now results just go into LQFB and can be seen there with a LQFB frontend.)
 * You can `grep` the code for "TODO" to see if there's something else that
   could be done.

Householding
------------

The tasks in this list do not require any mathematic wizardry, but are quite
important. Some of them will require constant help, so if you are looking to
become a long-time contributor, feel free to pick one of these as your own.

 * We urgently need automated testing / unit testing. Note that partially
   this has to be testing the HTML interface too, not just unit testing of
   python functions.
 * Keeping the patch in liquid_feedback_patch/ up to date against new versions
   of Liquid Feedback Core.
 * Create an easy installation script of Solon (one step + configuration)
 * To be honest, Liquid Feedback itself is a pain to install. It would help many
   people if you want to simplify that.
 * If you can think of items to add to this list, that is helpful in itself...


High-level architecture
-----------------------

TODO: I will draw some SVG soon.

The Acquisti algorithm relies on a group of servers, each operated by an 
independent Voting Authority, to communicate together. Threshold cryptography.
Also see next section about the bulleting board. It's all group communication.

Clients of course need to be under full control of their end users / voters - 
this is the same as for using any other crypto. Hence the client cannot simply 
be a web page, it needs to be some form of app/executable run on the client side.


The Non-erasable Public Bulletin Board
--------------------------------------

The Acquisti paper relies on an abstract device know as a *non-erasable public 
bulletin board*.

    Chaum [Cha81] introduced the concept of a bulletin board, a public broadcast 
    channel with memory where a party may write information that any party may 
    read. Since then, bulletin boards have been often used in election schemes. 
    All communications with the bulletin board are public and therefore can be 
    monitored. In the application we consider, no party can erase any data.

While such an element has been commonly used in cryptography for over 30 years 
now, papers typically leave undefined how to actually implement such a magic
bulleting board.

Since the rest of the Acquisti algorithm is based on threshold cryptography
among a group of independent voting authorities, it seems like an obvious idea
to also implement the non-erasable board as some form of distributed storage
among a cluster of servers, each of which is run by one of the authorities. This
could be implemented either with a message queue or some form of group 
communication library:

 * Allowed operations are insert and read. Update and delete are specifically
   *not* allowed.
 * A message is received by the bulleting board when a majority/threshold of
   the nodes in the cluster acknowledge receipt of it.
 * Clients probably need to read from multiple / majority of nodes to verify 
   that they get the correct message on read. In fact they also need to verify
   that an inserted message was correctly inserted.

Implementing this bulletin board is a nice non-trivial task that doesn't yet
include any cryptography.

Crypto
------

The Acquisti algorithm reuses many well known cryptographic functions as steps
in the protocol. For example RSA/PGP and El-gamal can be used as building 
blocks. Otoh there is also a need to implement some functionality from scratch,
using biginteger math (which python seems to support out of the box?).

The individual steps are not listed here, you need to read the [paper] and
go from there.

[paper]: http://www.heinz.cmu.edu/~acquisti/papers/acquisti-electronic_voting.pdf "Receipt-free Homomorphic Elections and Write-in Ballots, Alessandro Acquisti. Technical Report 2004/105, International Association for Cryptologic Research, May 2, 2004."