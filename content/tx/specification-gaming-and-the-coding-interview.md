---
title: "Specification Gaming and the Coding Interview"
date: 2019-05-06T10:00:00-04:00
draft: false
type: post
dek: "Are LeetCode-style interviews helping companies hire the right
candidates or has the specification drifted from the goal?"
---

[Specification gaming][0] is the unexpected exploitation of the conditions of a
test by an agent to satisfy it without actually completing the intended
objective. Processes evolve, side-stepping their creators’ motivations, and
instead work to maximize their performance in the environment in which they’ve
found themselves operating. [_The Surprising Creativity of Digital
Evolution_][1] outlines examples in the wild of algorithms exhibiting surprising
behaviors by either coming up with novel solutions or abusing latent bugs in the
scenario in which it was being trained to maximize a score from a fitness
function. In many of these cases the algorithm compromises the intended goal,
but like Skynet in _The Terminator_ films destroying humanity to continue
its mission of "safeguarding the world," they precisely follow the
specification.

<blockquote class="twitter-tweet" data-cards="hidden"
data-conversation="none"><p lang="en" dir="ltr">I hooked a neural network up to
my Roomba. I wanted it to learn to navigate without bumping into things, so I
set up a reward scheme to encourage speed and discourage hitting the bumper
sensors.<br><br>It learnt to drive backwards, because there are no bumpers on
the back. <a
href="https://t.co/8PdR3p6ePZ">https://t.co/8PdR3p6ePZ</a></p>&mdash; Custard
Smingleigh (@Smingleigh) <a
href="https://twitter.com/Smingleigh/status/1060325665671692288?ref_src=twsrc%5Etfw">November
8, 2018</a></blockquote>

Poor robot. It just wanted to do well and please its creator and didn’t try to
destroy humanity even once.

These unexpected outcomes are the result of emergent behavior from unsupervised
optimization by actors in a complex system training to maximize positive
feedback whose source had either drifted from or had not been seamlessly aligned
with the intended goal. In effect, the Roomba's operator had created conditions
in the training scenario that unintentionally asked it to drive backwards.

_Meanwhile_, at the same companies generating hype about the limitless promise
of artificial intelligence, engineers work to help their teams build their ranks
through coding interviews. The coding interview is a practical examination of a
candidate’s ability to write software by posing a problem to be solved
extemperaneously. These interviews are a rite of passage for software developers
and an obsessed-over hurdle: the only thing between you and that dream job is a
blank whiteboard, a smiling-albeit-standoff-ish bespectacled man in khakis, and
a binary tree that, you’re told, needs to be inverted. Something akin to the
intern qualification exercise in _The Social Network_, sans the hooting
audience, problematic binge drinking, and Sorkin dialogue.

As of this writing, the #1 bestseller on Amazon.com in the [computer science
category][2] is [Cracking the Coding Interview][3]. Widely referred to by its
initials by college students and job seekers of all ages, CTCI promises to teach
you everything you need to know to perform well in technical interviews at
companies like Google, Facebook, et al, via CliffsNotes on computer science
topics and 189 example problems and their solutions. It’s a prominent example of
a crop of books and startups that have been created to service a market of
software engineering hopefuls eager to cram by practicing questions similar to
those they can expect on an interview.

What sort of questions are we talking about? Let’s look at an example. Say
you’re given a list of numbers `[1, 9, 1, 9, 2]` and you're asked to find
the number which is in the list only once. Before thinking about it, you'd
likely scan this sequence and find that the only number without a duplicate is
2. Splendid. Now, how do you tell a computer to do the same thing?

You could tell the computer to do it the same way you might have done it
mentally. For each number, loop through the other numbers, count the number of
occurrences, and return the number that only occurs once. This will work quite
handily for lists like the one above. With a list of 5 items, this might result
in 25 operations of reading the list. Done, right? Well, if I gave you a list of
50,000 numbers, this approach is no longer viable. You can’t physically go
through 50,000 items for every item looking for duplicates, and your computer
will have to run 2.5 billion operations in order to produce a result for you.
This is a brute force approach to solving the problem.

Fear not. Better, less obvious solutions to this problem exist. One approach is
to take advantage of bit manipulation and the exclusive-or operation.  Any
number XOR zero will equal the original number, and any number XOR itself will
equal zero. Thus, if you start with zero, iterate through each number in the
list, and XOR them all against that number, you will be left with the number
without a duplicate. For this solution, your computer only evaluates each number
in the list once and stores only the running tally in memory. Linear time,
constant space. Cut the check.

That’s an easy, contrived example. [Leetcode][4] is a website that provides a
thousand such questions and an interactive environment to test your prowess
against a battery of test cases and the time and space efficiency of other
users’ solutions. It touts itself as a platform to help people enhance their
skills in order to prepare for technical interviews. The [question on Leetcode
currently holding the lowest success rate][5] asks a programmer to validate
whether a given string (such as `12` or `-54.9e33` or `bicycle`) can be
interpreted as a decimal number. Provided solutions in the discussion forum for
this question range from using Deterministic Finite Automata to parse the
string, to regular expressions gymnastics, to “`return !isNaN(parseFloat(s))`
lol.”

The internet gives us a window into this subculture of career hacking and
coding interview preparation. On forums like [/r/cscareerquestions][6], job
seekers talk about engaging in mock whiteboard interviews with each other and
studying these kinds of problems for hours a day as “grinding Leetcode.”
Leetcode organizes their questions by subject area such as breadth-first
search, or linked lists, or dynamic programming, and by company. Premium
subscribers are given access to the top N questions purportedly asked by top
tier technology companies.

Some argue that these coding interviews are far better than the previous trend
of why-is-a-manhole-cover-round-style brainteasers in that at least these
exercises are practical with well understood answers. Opponents argue that these
exercises are too far removed from the quotidian work of software development to
be useful, and perhaps even somewhat discriminatory as not all candidates could
possibly devote the required “grinding” time to prepare appropriately.

There’s something symmetric and satisfying about technology companies that pride
themselves on relentless optimization having their own interview processes
relentlessly optimized by job candidates. These filters are, at best,
identifying the right, motivated candidates, or at worst, creating a generation
of mercenaries with a mentality that to achieve a high paying job in
technology one must learn the test and game the specification.

The top all time post on [/r/cscareerquestions][6] is titled "[Accidentally
destroyed production database on first day of a job, and was told to
leave...][7]" It recounts the experience of a new graduate following an
environment setup document that included production database credentials which
they used to run a series of commands starting with `DROP DATABASE`. The veneer
of the coding interview suggests to candidates that hiring managers are wisened
monks who spent a lifetime in study for this moment to test your mettle. It
belies the reality: that same hiring manager is going to give you a Word
document with production database credentials on your first day and no amount of
practicing dynamic programming questions will prepare you for that.

Trends in hiring move slowly and while anxiety and backlash over the coding
interview continues to build, there isn't much in the way of data to show if
these interviews are effective or otherwise. For professional developers some
navel gazing and soul searching is warranted: is this tool identifying the right
candidates or are we unintentionally asking people to drive backwards?

[0]: https://vkrakovna.wordpress.com/2018/04/02/specification-gaming-examples-in-ai/
[1]: https://arxiv.org/pdf/1803.03453.pdf?
[2]: https://www.amazon.com/Best-Sellers-Books-Computer-Science/zgbs/books/3508
[3]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/0984782850/ref=zg_bs_3508_1?_encoding=UTF8&psc=1&refRID=TTX7Z2JWRFQ8SWBBS3TD
[4]: https://leetcode.com
[5]: https://leetcode.com/problems/valid-number/
[6]: https://reddit.com/r/cscareerquestions
[7]: https://www.reddit.com/r/cscareerquestions/comments/6ez8ag/accidentally_destroyed_production_database_on/
