Solon Voting
============
A cryptographically secure voting system.

Introduction
------------

Solon is a system that provides cryptographically secure electronic voting 
(e-voting). In particular, it implements the so called [Acquisti] algorithm. See
"Reading list" below for a list of academic articles on cryptographically 
secure voting.

[acquisti]: http://www.heinz.cmu.edu/~acquisti/papers/acquisti-electronic_voting.pdf 

Solon is designed to be used together with burgeoning direct democracy and 
delegated democracy systems. These typically have requirements that go beyond
your average government elections. In particular, the voting algorithm needs
to support the possibility to flexibly delegate (and un-delegate) your vote,
and it must be possible to use more advanced vote counting, such as Schulze
method and other preferential voting methods.

Currently, we focus on providing a cryptographically secure voting facility
that connects with [Liquid Feedback]. However, the code is modular in this
respect, and in the future we will be able to support any other similar system.
See "Direct democracy platforms" for other systems we like to keep an eye on.

[liquid feedback]: http://liquidfeedback.org/

When ready, Solon could also be used to implement e-voting for traditional
elections or referendums. As far as we know, none of the e-voting systems
being sold for such purposes implement a cryptographically secure voting
algorithm (despite what their marketing might claim), so even if this is not
the primary motivation behind Solon, it would be an improvement over the 
systems currently used.


Status
------

Solon is currently in active development, and it is not ready to be used yet.

The current code is merely a mockup that is able to connect with a Liquid 
Feedback system, extract issues to vote on and return results back. It is just
a mockup that demonstrates the data flows. The actual code for any cryptographic
functionality is completely missing - implementing this is where our focus is 
next.

If you want to contribute to development, join us on [Github]! The Solon
developers think it's an exciting prospect to take representative democracy to
the next level, and if you feel the same, you are welcome to join. See 
"Contributing" below for more info.

[github]: https://github.com/henrikingo/solon-voting


Installation
------------

### Install Liquid Feedback

You need a working Liquid Feedback installation to use Solon. Not only that,
but you need a patched version of Liquid Feedback Core. Let's start with that:

0) Tip: Liquid Feedback supports Debian Squeeze. If you are on any other platform,
it's probably worth taking that seriously: try using Vagrant or VirtualBox to 
install Liquid Feedback into a Debian Squeeze instance.

1) Download Liquid Feedback Core 2.0.11. This is currently the only version
against which we provide a patch, newer versions will probably not work.
`wget http://www.public-software-group.org/pub/projects/liquid_feedback/backend/v2.0.11/liquid_feedback_core-v2.0.11.tar.gz`

2) Untar: `tar xvf liquid_feedback_core-v2.0.11.tar.gz` and 
`cd cd liquid_feedback_core-v2.0.11/`

3) Get the patch: `wget https://raw.github.com/henrikingo/solon-voting/liquid_feedback_patch/liquid_feedback_core-v2.0.11.solon-v0.1.diff`

4) `patch -p1 < liquid_feedback_core-v2.0.11.solon-v0.1.diff`

5) You can now follow the instructions from 
http://dev.liquidfeedback.org/trac/lf/wiki/installation to install the full
Liquid Feedback system. When it is time to install the Core module, use the
directory you have just patched with the Solon enabling patch.

6) Finally, you need to configure Liquid Feedback to run in the 
ext_voting_service mode so that Solon can hook into your voting procedure:
    su - www-data
    psql -c "INSERT INTO system_setting (member_ttl, ext_voting_service) VALUES ('1 year', TRUE);" liquid_feedback
    psql -c "UPDATE system_setting SET ext_voting_service=TRUE;" liquid_feedback
    exit
(Note that if the second command above fails, that's ok.)

You can now browse Liquid Feedback via a web browser. When you login as 
administrator, there's a very small "Admin" link at the bottom of the page. You
can use that to create new users and organizations and policies that are used
for issue management.

### Install Solon itself

7) `git clone https://github.com/henrikingo/solon-voting.git`

8) `cd solon-voting`

9) You need to create another PostgreSQL database to be used by Solon. (Note:
The current Solon is just a mockup without cryptography, so we call the module
"dummy" and hence we tend to use the database name "solon_dummy".) Here we use
the same user "www-data" as was used in the instructions for Liquid Feedback:
    su - www-data
    createdb solon_dummy
    createlang plpgsql solon_dummy
    psql -v ON_ERROR_STOP=1 -f sql/dummy.sql solon_dummy
    exit

10) Optionally, edit the configuration file `config.py`. (If you didn't change
anything in the above instructions, such as usernames or passwords, then you're
good to go and don't need to change anything in the config.)

11) Start the webserver: `su www-data -c 'python solon_server.py'`

Now you can open a web browser and go to localhost:8888. Hit the "execute cron 
tasks" link to see if you can fetch your first issues from Liquid Feedback to
vote on.


Contributing
------------

If you are exited about the prospect of taking representative democracy to the
next level, then you might be interested in joining us. Check out the code at
https://github.com/henrikingo/solon-voting 

Solon might be especially interesting if you are into cryptography or math, as 
we need to implement a few novel cryptographic functions along the way. But the 
project isn't exclusive to math geeks! There are a number of skills from Python,
HTTP, JSON and automated testing where your help is welcome.

Please read the file [TODO.md] for an idea on tasks where contributions are 
especially welcome.

[TODO.md]: https://raw.github.com/henrikingo/solon-voting/master/TODO.md

Right now, there is no mailing list yet. You can email me at 
henrik.ingo@avoinelama.fi.

You may also follow, and @mention or DM, [@SolonVoting] on Twitter.

[@SolonVoting]: https://twitter.com/solonvoting

Reading list
------------

Note: Unless you enjoy reading papers stuffed with university level math, some
of these links may not be for you. It is still possible to contribute to Solon
even if you don't want to torture your brain cells with the actual cryptography.
If you do enjoy university level math, brace yourself, because the good stuff
is about to begin:

Solon will implement the Acquisti e-voting scheme. It is described in the
paper [Acquisti].

[acquisti]: http://www.heinz.cmu.edu/~acquisti/papers/acquisti-electronic_voting.pdf "Receipt-free Homomorphic Elections and Write-in Ballots, Alessandro Acquisti. Technical Report 2004/105, International Association for Cryptologic Research, May 2, 2004."

As it happens, it has been implemented in software once already. The process is
described in [Goulet]:

[Goulet] http://www.seas.upenn.edu/~cse400/CSE400_2004_2005/34writeup.pdf "Surveying and Improving Electronic Voting Schemes, Jonathan D. Goulet, Jeffrey S. Zitelli, Sampath Kannan, 2005."

This paper is a good overview of the field of cryptographic e-voting protocols
and the requirements such a protocol should meet. It concludes that the Acquisti
protocol is the most complete solution (at the time of its writing, of course).
Even if you don't want to read about the math involved, I recommend you read at 
least the beginning of this paper. The introduction in this paper is useful to 
everyone who want to get an overview of e-voting protocols.
[Sampigethaya]

[Sampigethaya]: http://www.ee.washington.edu/research/nsl/papers/JCS-05.pdf "A framework and taxonomy for comparison of electronic voting schemes, K Sampigethaya, R Poovendran, Computers & Security, Elsevier 2006."

The following papers reference the Acquisti paper and provide some critique. I
have not yet read them in detail myself: [Meng], [Meng2]

[Meng]: http://people.scs.carleton.ca/~clark/biblio/coercion/Meng%202010.pdf "A Receipt-free Coercion-resistant Remote Internet Voting Protocol without Physical Assumptions through Deniable Encryption and Trapdoor Commitment Scheme, Bo Meng, Zimao Li and Jun Qin. JOURNAL OF SOFTWARE, VOL. 5, NO. 9, SEPTEMBER 2010."
[Meng2]: http://www.academypublisher.com/proc/iscsct10/papers/iscsct10p148.pdf "Automatic Verification of Acquisti Voting Protocol in Formal Model, Bo Meng, Wei Huang, and Dejun Wang. Proceedings of the Third International Symposium on Computer Science and Computational Technology(ISCSCT ’10) Jiaozuo, P. R. China, 14-15,August 2010, pp. 148-150."

Btw, if you are interested in the concept of delegated democracy, here are a few
links:

 * http://en.wikipedia.org/wiki/Delegative_democracy
 * http://liquidfeedback.org/mission/
 * http://openlife.cc/DirectDemocracy


Direct democracy platforms
--------------------------

Initially, we focus on making Solon work together with [Liquid Feedback].

[liquid feedback]: http://liquidfeedback.org

These platforms also have some form of direct democracy or delegated democracy
potential in them:

 * [Popvox] (https://www.popvox.com/ "Popvox")

We might try to support them in the future, but for now it's just interesting
to keep an eye on how this area develops.

Frequently Asked Questions
--------------------------

See [docs/FAQ.md]

[docs/FAQ.md]: https://raw.github.com/henrikingo/solon-voting/master/docs/FAQ.md


LICENSE
-------

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

