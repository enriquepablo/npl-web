
Motivation
==========

The **npl** language may be viewed
as a proof of concept for the solution of a problem
that has been present in logics
since Gottlob Frege uncovered it.
Here I will try to introduce this problem
and my proposed solution.


Historical introduction
-----------------------

Frege
~~~~~

Predicate logic was first developed
by Gottlob Frege.
He developed it
in the belief that he was unraveling
the foundations of
the natural logic behind scientific theories
(or behind any unambiguous text
expressed in a natural language)
(see `this <http://plato.stanford.edu/entries/frege/#BasFreTerLogPreCal>`_).
He pursued a mathematical formalism
capable of expressing
any informal scientific theory.
To that end,
he developed predicate logic and,
at the same time and on top of it,
a particular formal theory
which we may call "theory of concepts".
In this theory he had individuals (or values)
and he had classes (or value-ranges),
connected through class predicates (belongs, subclass).
He also had an unlimited amount
of other predicates
(what he called concepts).
With this,
he very nearly had
all the elements he needed
to have a formal system
with the same expressive power
as the natural languages.
With his class predicates
he was able to represent
the class relations in the natural languages,
generally expressed in them through copular verbs;
and with his concept/predicates
he was able to represent
the rest of relations expressed in the natural languages
through non-copular verbs.

However, there was a problem with this scheme.
His theory was second order,
so he had 2 types of variables:
those that range over individuals,
and those that range over concepts.
This, in itself, was not satisfactory.
In the natural languages
you do not have 2 types of variables,
but just one,
that can range over anything.
I think that this can be easily seen with an example.
Let us examine the sentence:
"if Mary wants something, she gets it".
I will take that this sentence is equivalent to
"for all X, if mary wants X, mary gets X".
So, we have constructs
in the natural languages that
behave very much like variables.
And "to go to the cinema" can fall under X,
so predicates can be in the range of variables.
And also "an apple" can fall under the same X,
which would correspond to an individual;
so variables are first order,
there is only one order of variables.

To bridge this gap,
Frege had his Basic Law V,
that basically established a correspondence
between classes and concepts.
This allowed him to unify
the use of his 2 orders of variables.
But along came Bertrand Russell,
showing that this axiom leads to contradictions
and must be dropped.
This was a heavy blow for Frege,
who felt that his project had failed,
and never quite recovered from it.

His failure was however a very productive one,
and predicate logic became a hugely successful technology.
After Russell's antinomies,
predicate logic developed in 2
separate but interconnected ways.
On one hand, logicists,
following the lead of Zermelo,
developed a first order set theory
with which they provided a foundation for most mathematics.
On the other hand,
Russell, and the logical positivists,
pursued the development of higher order logics
with the original aim of Frege,
of developing a formal system
with the expressive power of the natural languages.
They did not succeed in this effort.

First order logic
~~~~~~~~~~~~~~~~~

The problem encountered by Frege
can also be seen in first order logic.
To show this,
we will imagine a first order set theory,
with 3 predicates: "equals", "belongs to", and "is a subset of".
In principle, this theory will have 3 axioms,
of equality, extensionality, and definition of subsets.
This axioms provide the predicates with the basic form
of class/set relations,
and can thus be put in correspondence
with the natural usage of the copular verbs.
We then have a formal system
where we can express any scientific taxonomy, and reason about it.
Obviously this is not enough to express the logic of any
scientific theory; we need other predicates apart from copular ones,
to represent other relations apart from that of belonging to classes.

First order logic allows us to use more predicates, of course.
We can define new predicates and give them whatever form we like through
additional axioms. Nevertheless, there is still one problem to overcome
if we aspire to a mechanization of the scientific use of the natural
language. And that is the ability to have variables that range over
predicates, the ability to express predicates through constraints,
classes of predicates, etc. In first order predicate logic,
you cannot have variables ranging over predicates.
In the natural language, I hope I have shown
that you can do the equivalent.

The natural way out of this problem is
an axiom schema of unrestricted comprehension,
that (like Frege's Basic Law V)
establishes a correspondence between
predicates and sets (thus classes).
But if we add UC to the system,
Russell's paradox apply,
that same paradox that toppled Frege's edifice.
As Paul Bernays stated
in the introduction to his "Axiomatic set theory" [1]:

"What really is excluded by the antinomies is only
that interpretation (easily suggested at first)
of set theory which finds its formal expression
in the calculus called newly Idealkalkül by
Hermes and Scholz, whose domain of individuals
contains for every predicate ``B`` an assigned
individual ``p`` such that::

    (x)(x € p <-> B(x)).
"

So with first order logic
we come to exactly the same point
where Frege foundered.

Modern manifestations of the problem
------------------------------------

This problem I have described can be seen in many modern
logic programming systems, where any attempt to use
our natural logic as design model for software development is futile.

A paradigmatic example is
the OWL language of the semantic web.
This language had two flavours: DL, and full (well, and lite).
It is an ontology language
that can be processed by reasoners
to extract consecuences.
It has basic class predicates,
and it has have UC:
you can have
anonymous classes defined by predicates.
But, in the DL flavour,
you cannot treat classes as individuals:
you cannot have variables ranging over them.
In the full flavour,
you can,
but you are not guaranteed
that the system will be
consistent under a reasoner.
And it is the full flavour
that would provide full (natural) expresivity.

A possible solution
-------------------

My proposition is to use the predicates of set theory
to express the natural copular verbs,
just as Frege (or OWL) did,
but then, as we shall see below,
instead of representing the rest of the natural verbs
as formal predicates,
we represent them through individuals of the theory.
We limit our (first order) theory to only have the
basic predicates of set theory in their barest form.
With their barest form,
I mean defined by just equality, extensionality
and a definition of subset,
and perhaps some boundary axioms
to define a universal and an empty "set".
Paul Bernays, in the text quoted above,
after dismissing UC,
goes on to provide a number of additional axioms,
like the axiom of choice or the axiom of infinity,
what he called constructive axioms.
But his aim was different from ours.
He wanted a formalism to base on it
the whole of mathematics,
so he needed a few axioms to produce a finished theory
that would contain all mathematical structures.
We do not need a closed theory,
nor an initial complex structure
as model for our theory.
All we need is a simple starting theory,
that allows us to extend it with
ad hoc new individuals and axioms
to model each particular informal theory.

The theory NPL
~~~~~~~~~~~~~~

We call this theory NPL.
To sketch it, we will only use implication ``->``
and conjunction ``&``
as logical connectives,
and the only production rule will be modus ponens.
Variables are denoted by ``x1``, ``x2``...
and are always universally quantified in their outernmost scope (sentence);
and individuals are denoted by any sequence of lower case letters.
The predicates are ``isa``, equivalent to "belongs to",
and ``are``, equivalent to "is a subset of"
(for this quick sketch of the theory, we do not need equality).
We use these predicates in an infix form,
and we have that::

  x1 isa x2 & x2 are x3 -> x1 isa x3

  x1 are x2 & x2 are x3 -> x1 are x3

Now to the representation of natural verbs other than copulas.
For simplicity, we will only consider natural verbs that represent
binary relations, so a natural sentence with such a verb would have
the form of a triplet subject-verb-object.
To represent this relation, we use a ternary operator ``f``
(from fact). So, a non-copular sentence, in our system, would
have the form ``f(s, v, o)`` (where ``s``, ``v``, and ``o`` are just
individuals of the theory).
Since ``f`` is an operator, this
sentence stands for just another individual of the theory, and has
no truth value.
We will call this sort of individuals "facts".
To attach truth value to facts, we use the set predicates,
to relate them with another individual of the theory,
``fact``. So a complete non-copular sentence, in this theory,
would have the form (with prefix operators and infix predicates)::

  f(s, v, o) isa fact

Since we only have 2 (or 3, with equality) formal predicates,
we do not need UC at all,
and yet we can have variables that range over the equivalents of
our natural verbs (and also over whole "facts").
The point is that we can model the forms of natural logic
with very few predicate and operator symbols,
and that any new term we may want to introduce,
when modelling any kind of natural discourse,
will be quantifiable by first order variables.
Those symbols that can not be quantified,
like ``are`` or ``isa`` or ``f``,
are so few that do not merit to be so.

We can be even more fine-grained. If we call "predication" to a
pair verb-object, we may want to have variables that range over
them. To do this, we can define a new operator ``p``, that produces
predication individuals, so that now the ``f`` operator takes 2 operands,
the subject and a predication, to have something like::

  p(v, o) isa predication

  f(s, p(v, o)) isa fact

And, to show a little more of the power that we can obtain from
such a system, note that facts and predications are individuals
of the theory, so we can use them where we have used ``s`` or ``o``,
to build as complex a sentence as we may want (I think it wouldn't make
much sense to use them in place of ``v``).

An example derived theory
~~~~~~~~~~~~~~~~~~~~~~~~~

An example developed on top of this theory might be (using a primitive
universal set ``word``)::

  person isa word

  man are person

  john isa man

  woman are person

  sue isa woman

  verb isa word

  loves isa verb

  x1 isa person &
  x2 isa verb &
  x3 isa person &
  f(x1, x2, x3) isa fact
  ->
  f(x3, x2, x1) isa fact

Now, ``john loves sue`` will imply that ``sue loves john``.


There is a semantics for this theory `here <http://enriquepablo.github.com/nlproject/NL.html>`_.
