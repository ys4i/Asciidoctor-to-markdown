title: KiCad Developer Culture weight: 6 summary: The guidelines
describing how the lead developer team interacts with each other. ---

# Purpose

This document lays out the desired ways of working for the KiCad lead
development team. It is meant to provide a basic introduction for new
developers as well as a reference for current lead developers in how to
manage and resolve uncertainty and conflicts.

## Base Guidelines

- We are a team of equals.

- We respect our userbase.

- We respect each others’ contributions.

- The code we contribute belongs to everyone.

- In a disagreement, everyone gives a little.

- We prioritize our families, our health and our offline lives ahead of
  KiCad.

# Discussion

## We are a team of equals

This means that your opinions will be given equal weight with every
other person on the lead developer team.

In the event a decision is deadlocked after multiple developers have
weighed in, the resolution will be made by the Project Lead. This is,
however, an outcome to be avoided if at all possible.

We also recognize that we each bring different experiences to the team.
We will try to give weight to the lead developers who actively use a
particular feature when deciding on implementation issues.

## We respect our userbase

We all come to this project, developers and users, with differing
expectations, experiences and english fluency. When dealing with each
other, it is inevitable that these differences will lead to conflict
over politeness, appropriate expectations or any of the myriad other
*impedance mismatches* we might have in our communications.

As lead developers, our behavior in public project spaces reflects on
the project as a whole. We will hold ourselves to a higher standard in
our behavior than the standard to which we hold the end users.

We will not respond to any issue report, merge request or forum post
when we feel annoyed or irritated by the person posting or by the
content of the post. We provide a Zulip channel where lead developers
can tag others to take on the burden of calmly responding. We will do
the following:

1.  Post a link to the page in question

2.  (optional) Express any frustrations we have

3.  Say why this content is wrong

\(1\) and (3) are required. (2) is optional but available if it helps to
diffuse the emotion around the content. At this point, another,
disinterested lead developer will take on the task of responding. You
get to go back to coding other things.

Failing to follow this guideline will inevitably lead to drama. And
drama sucks time from both you, the other person and the rest of the
lead developer team as we end up dealing with the response and not the
actual content.

## We respect each others’ contributions

While we all make mistakes, we will not revert the contributions of
other developers without their explicit consent, even when we think that
they might not have implemented something in accordance with our views
of the policies.

We each are responsible for the quality of our own contributions. If
there is a disagreement over a commit, it must be worked out over
e-mail/Zulip/video call/etc with the person committing the code. We do
not fight in the code base.

In the event that a problematic commit has been made to the codebase and
the person responsible is not available to discuss it, then the commit
can be reverted after publicly (either on GitLab or Zulip) discussing
and agreeing on this with the rest of the lead development team.

When we disagree with a contribution made by another developer, we will
address it directly with them politely. Examples of polite feedback
might include:

> This code doesn’t work for me when I…

Or

> I think that this code will cause problems because…

Examples of rude feedback that we will avoid include:

> This code stinks/sucks/is really bad/etc

Or

> Whoever screwed up this part of the codebase…

Naturally, we are all welcome to provide ourselves with as much impolite
feedback on code we have written as we can handle. :)

## The code we contribute belongs to everyone

We are a free/libre open source community. Everything that we contribute
to the KiCad project belongs to and is the responsibility of the whole
KiCad development team. Copyrights on new files will be assigned to the
KiCad Development team. They may be additionally assigned to a primary
author or sponsor of the code that is written, but this does not mean
that the original author has veto power or even greater weight in the
discussions of how the code develops.

When we commit code to the KiCad codebase, we are standing on the
shoulders of everyone who has done so before us. Our contribution will
be changed, updated, refined and polished in ways that we did not
anticipate and that we might not even agree with. That process is a
compliment to the author of the original code because the changes would
not have been possible without the original work.

## In a disagreement, everyone gives a little

We will frequently come to a point in discussion of either the
specification document or code itself where we each have a different
opinion about the direction of a particular feature or other
implementation detail. When this happens, we work to prioritize the
elements that are most important to each developer.

We decide which parts of a feature are more important to us and ask for
those parts to exist, giving up on the parts that, while still very
important, are less critical than others. Everyone is expected to
moderate their positions to come to a consensus.

We offer our opinions in ways that make it known how important the
opinion is and why. For example:

> It is very important that I am able to modify the start point of lines
> and arcs in the same way because I need this to be a unified interface
> for XYZ

Or

> I have a preference for the print dialog being non-modal so that I can
> see the preview update in real time but it is not a strong one.

It is important to assess our own priorities when entering a
conversation with other developers. Not every opinion can be the top,
number 1 priority.

<div class="note">

Each developer on the team is highly skilled and if more than one is
disagreeing with you, it is always a good idea to step back and examine
if your opinion might be out of place or need to be changed.

</div>

We will give extra consideration to the concerns raised by the
developers who actively use a feature and those that are willing to
implement their own vision.

If the developers involved in the discussion cannot agree, after each
has moderated their position to accommodate an agreement, the final
decision will be made by the project lead. Involving the project lead in
resolving disputes is to be avoided if at all possible.

## We prioritize our families, our health and our offline lives ahead of KiCad

KiCad is a wonderful place to contribute to the community. We strive to
be open and engaged with each other. We try to laugh with each other and
support one another. But we are still just an online group of
like-minded people. We will not place pressure on each other to work
more or longer. We will encourage each other to take breaks from coding.
We will watch for signs of overwork or stress and encourage each other
to care for our own wellbeing before the wellbeing of the codebase.

We will design our systems and procedures in such a way that facilitates
anyone needing to take an unexpected leave of absence.
