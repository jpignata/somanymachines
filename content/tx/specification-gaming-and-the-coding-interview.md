---
title: "Specification Gaming and the Coding Interview"
date: 2019-05-06T10:00:00-04:00
draft: false
type: post
---

[Specification gaming][0] is the unexpected exploitation of the conditions of a
test by an agent to satisfy it without actually completing the intended
objective. Processes evolve, side-stepping their creators’ motivations, and
instead work to maximize their performance in the environment in which they’ve
found themselves operating. This is an emerging stream of study in artificial
intelligence where [recent papers][1] outline examples of digital evolution in
the wild. Many of these examples are algorithms exhibiting surprising behaviors
by either coming up with novel solutions or abusing latent bugs in the scenario
in which it was being trained to maximize the score from a fitness function.  In
many of these cases the algorithm compromises the intended goal. Like Skynet
from the _The Terminator_ films destroying humanity to continue its mission of
"safeguarding the world" these processes precisely follow the specification of
the test.

Take another example:


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

Poor robot. He just wanted to do well and please his creator and didn’t try to
destroy humanity even once. What a good boy.

These unexpected outcomes are the result of emergent behavior from unsupervised
optimization by actors in a complex system training to maximize positive
feedback whose source had either drifted from or had not been seamlessly aligned
with the intended goal. In effect, the Roomba's operator had created conditions
in the training scenario that asked it to drive backwards.

_Meanwhile_, at the same companies delivering this new world of artificial
intelligence, engineers work to help their teams build their ranks through
coding interviews. The coding interview is a practical examination of a
candidate’s ability to write software by posing a problem to be solved
extemperaneously. These interviews are a rite of passage for software developers
and an obsessed-over hurdle: the only thing between you and that dream job is a
blank whiteboard, a smiling-albeit-standoff-ish bespectacled man in khakis, and
a binary tree that, you’re told, needs to be inverted. Something akin to the
intern qualification exercise in _The Social Network_, sans the hooting
audience, problematic binge drinking, and Sorkin dialog.

The current #1 bestseller on Amazon.com in the [computer science category][2]
is [Cracking the Coding Interview][3]. Widely referred to by its initials by
college students and job seekers of all ages, CTCI promises to teach you
everything you need to know to perform well in technical interviews at
companies like Google, Facebook, et al, via CliffsNotes on computer science
topics and 189 example problems and their solutions. It’s a prominent example of
a crop of books and startups that have been created to service a market of
software engineering hopefuls eager to cram by practicing questions similar to
those they can expect on an interview.

What sort of questions are we talking about? Let’s look at an example. Say
you’re given a list of numbers like: `[1, 9, 1, 9, 2]` and you're asked to find
the number which is in the list only once. Before thinking about it, you'd
likely scan this sequence and find that the only number without a duplicate is
2. Splendid. Now, how do you tell a computer to do the same thing?

You could tell the computer to do it like you did it mentally. For each number,
loop through the other numbers, count the number of occurrences, and return the
number that only occurs once. This will work quite handily for lists like the
one above. With a list of 5 items, this might result in 25 operations of reading
the list. Done, right? Well, if I gave your computer (or you) a list of 50,000
numbers, this approach is no longer viable. You can’t physically go through
50,000 items for every item looking for duplicates, and your computer will have
to run 2.5 billion operations in order to produce a result for you. This is a
brute force approach to solving the problem.

Fear not. Better solutions to this problem exist. For example, it can be solved
by taking advantage of bit manipulation and the exclusive-or operation.
Any number XOR zero will equal the original number, and any number XOR itself
will equal zero. Thus, if you start with zero, iterate through each number in
the list, and XOR them against that zero, you will be left with the number
without a duplicate. For this solution, your computer only evaluates each number
in the list once and stores only the running tally in memory. Linear time,
constant space. Cut the check.

That’s an easy, contrived example. [Leetcode][4] is a website that provides a
thousand such questions and an interactive environment to test your prowess
against a battery of test cases and the time and space efficiency of other
users’ solutions. The [question on Leetcode currently holding the lowest success
rate][5] asks a programmer to validate whether a given string (say, `12` or
`+54.9e33` or `bicycle`) can be interpreted as a decimal number. Provided
solutions in the discussion forum for this question range from using
Deterministic Finite Automata to parse the string, to regular expressions
gymnastics, to “`return !isNaN(parseFloat(s))` lol.”

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
technology one must learn the test and game the specification.  Despite
widespread techno-optimism and a boom-time job market, the [Bureau of Labor
Statistics projects computer programming to contract by about 7%][7] by 2026 due
to the portability of these jobs to locales with less expensive labor pools, and
an inevitable economic contraction or recession looms.

In a tighter job market will companies continue to rely on the coding
interview, or will we find we’ve been unintentionally asking people to drive
backwards?

[0]: https://vkrakovna.wordpress.com/2018/04/02/specification-gaming-examples-in-ai/
[1]: https://arxiv.org/pdf/1803.03453.pdf?
[2]: https://www.amazon.com/Best-Sellers-Books-Computer-Science/zgbs/books/3508
[3]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/0984782850/ref=zg_bs_3508_1?_encoding=UTF8&psc=1&refRID=TTX7Z2JWRFQ8SWBBS3TD
[4]: https://leetcode.com
[5]: https://leetcode.com/problems/valid-number/
[6]: https://reddit.com/r/cscareerquestions
[7]: https://www.bls.gov/ooh/computer-and-information-technology/computer-programmers.htm
