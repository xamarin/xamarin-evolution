# Xamarin Evolution Process

Xamarin is growing and evolving, guided by a community-driven process of evolution. This document outlines this process and how a feature grows from a rough idea into something that can improve the Xamarin development experience for millions of programmers.

## Scope

This process covers all changes to Xamarin, including new features and APIs (no matter how small), and proposals to change or remove existing features or APIs. Smaller changes, such as bug fixes, optimizations, or diagnostic improvements can be contributed via normal contribution process (i.e., bug reports and pull requests).

## Goals

The objective of this evolutionary process is to leverage the collective ideas, insights, and experience of the Xamarin community to improve the experience of developing with the Xamarin SDKs. Its two primary goals are:

* Engage the wider Xamarin community in the ongoing evolution of Xamarin, and
* Maintain the vision and conceptual coherence of Xamarin.

There is a natural tension between these two goals, and the evolution process aims to strike a balance that best serves the Xamarin community as a whole.

## Participation

Everyone is welcome to propose, discuss, and review ideas to improve
the Xamarin SDKs on the [appropriate mailing list][xamarin-evolution-mailing-list]. Before posting a review,
please see the section "What goes into a review?" below.

The Xamarin core team is
responsible for the strategic direction of Xamarin. Core team members
initiate, participate in, and manage the public review of proposals
and have the authority to accept or reject changes to Xamarin.

## What goes into a review?

The goal of the review process is to improve the proposal under review
through constructive criticism and, eventually, determine the
direction of Xamarin. When writing your review, here are some questions
you might want to answer in your review:

* What is your evaluation of the proposal?
* Is the problem being addressed significant enough to warrant a change to Xamarin?
* Does this proposal fit well with the feel and direction of Xamarin?
* If you have used other frameworks with a similar feature, how do you feel that this proposal compares to those?
* How much effort did you put into your review? A glance, a quick reading, or an in-depth study?

Please state explicitly whether you believe that the proposal should be accepted into Xamarin.

## How to propose a change

* **Check prior proposals**: many ideas come up frequently, and may be in active discussion on the mailing list.  Please check the mailing list archives and this list for context before proposing something new.

* **Socialize the idea**: propose a rough sketch of the idea on the [appropriate mailing list][xamarin-evolution-mailing-list], the problems it solves, what the solution looks like, etc., to gauge interest from the community.
* **Develop the proposal**: expand the rough sketch into a complete proposal, using the [proposal template](0000-template.md), and continue to refine the proposal on the evolution mailing list. Prototyping an implementation and its uses along with the proposal is encouraged, because it helps ensure both technical feasibility of the proposal as well as validating that the proposal solves the problems it is meant to solve.
* **Request a review**: initiate a pull request to the [xamarin-evolution repository][xamarin-evolution-repo] to indicate to the core team that you would like the proposal to be reviewed. When the proposal is sufficiently detailed and clear, and addresses feedback from earlier discussions of the idea, the pull request will be accepted. The proposal will be assigned a proposal number as well as a core team member to manage the review.
* **Address feedback**: in general, and especially [during the review period](#review), be responsive to questions and feedback about the proposal.

## Review process

The review process for a particular proposal begins when a member of
the core team accepts a pull request of a new or updated proposal into
the [xamarin-evolution repository][xamarin-evolution-repo]. That core team
member becomes the *review manager* for the proposal. The proposal
is assigned a proposal number (if it is a new proposal), then enters
the review queue.

The review manager will work with the proposal authors to schedule the
review. Reviews usually last a single week, but can run longer for
particularly large or complex proposals.

When the scheduled review period arrives, the review manager will post
the proposal to the [appropriate mailing
list][xamarin-evolution-mailing-list] with the subject "[Review]"
followed by the proposal title and update the list of active
reviews. To avoid delays, it is important that the proposal authors be
available to answer questions, address feedback, and clarify their
intent during the review period.

After the review has completed, the core team will make a decision on
the proposal. The review manager is responsible for determining
consensus among the core team members, then reporting their decision
to the proposal authors and mailing list. The review manager will
update the proposal's state in the [xamarin-evolution
repository][xamarin-evolution-repo] to reflect that decision.

## Proposal states
A given proposal can be in one of several states:

* **Awaiting review**: the proposal is awaiting review. Once known,
  the dates for the actual review will be placed in the proposal
  document and updated in the [review schedule](schedule.md). When the
  review period begins, the review manager will update the state to
  *under review*.
* **Under review**: the proposal is undergoing public review on a [xamarin mailing list][xamarin-evolution-mailing-list]. 
* **Under revision**: the proposal is undergoing revision by the
  author(s) based on feedback from the review.
* **Deferred**: consideration of the proposal has been deferred
  because it does not meet the [goals of the upcoming major Xamarin
  release](README.md). Deferred proposals will be reconsidered when
  scoping the next major Xamarin release.
* **Accepted**: the proposal has been accepted and is either awaiting
  implementation or is actively being implemented. Once a proposal
  enters the "accepted" state, it gets placed into its [target Xamarin
  release](README.md).
* **Rejected**: the proposal has been considered and rejected.

[xamarin-evolution-repo]: https://github.com/xamarin/xamarin-evolution  "Xamarin evolution repository"
[xamarin-evolution-mailing-list]: http://open.xamarin.com/community  "Xamarin evolution mailing list"

## Review announcement

When a proposal enters review, an email using the following template will be
sent to the [appropriate mailing list][xamarin-evolution-mailing-list]:

---

Hello Xamarin community,

The review of "\<\<PROPOSAL NAME>>" begins now and runs through \<\<REVIEW
END DATE>>. The proposal is available here:

> <https://github.com/xamarin/xamarin-evolution/blob/master/proposals/NNNN-proposal.md>

Reviews are an important part of the Xamarin evolution process. All reviews
should be sent to the xamarin-evolution mailing list at

> <https://lists.xamarin.org/mailman/listinfo/xamarin-evolution>

or, if you would like to keep your feedback private, directly to the
review manager. When replying, please try to keep the proposal link at
the top of the message:

> Proposal link:
>>  http://linkToProposal

>  Reply text

>>  Other replies

##### What goes into a review?

The goal of the review process is to improve the proposal under review
through constructive criticism and, eventually, determine the direction of
Xamarin. When writing your review, here are some questions you might want to
answer in your review:

* What is your evaluation of the proposal?
* Is the problem being addressed significant enough to warrant a
  change to Xamarin?
* Does this proposal fit well with the feel and direction of Xamarin?
* If you have used other languages or libraries with a similar
  feature, how do you feel that this proposal compares to those?
* How much effort did you put into your review? A glance, a quick
  reading, or an in-depth study?

More information about the Xamarin evolution process is available at

> <https://github.com/xamarin/xamarin-evolution/blob/master/process.md>

Thank you,

-\<\<REVIEW MANAGER NAME>>

Review Manager

---
