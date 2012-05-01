The npl language
================

In the use of **npl** there are basically 4 stages:

 * Define terms;
 * Build facts and rules with those terms, and add them to a knowledge base (a.k.a. kb);
 * Extend the kb to all logical consequences;
 * Query the kb.

In the example in the `home page <index>`_,
the first four lines are defining terms
(``person``, ``mike``, ``sue``, ``loves``)
using predefined terms (``thing``, ``verb``),
and the fifth to seventh lines are a fact and a rule
built from those terms.
If we extend the kb holding the program,
and query it,
we will find the, in this case, only conclussion: ``sue loves mike``.

Basic programming elements
--------------------------

**npl** provides three basic elements for writing programs:
atomic elements,
complex elements for adding to the kb,
and orders (commands) to manipulate the kb in different ways.

We also have macros,
but macros are another language built on top of **npl**,
and we will consider them `elsewhere <npl_macros>`_.

atomic elements
~~~~~~~~~~~~~~~

There are several types of atomic elements
with which to build complex elements.
The first distinction we make
is among logical and non-logical elements,
(following the terminology of first order logic).
Logical elements are all predefined,
identified by reserved words of the language.
Non-logical elements are defined by the programmer.

Among the logical elements,
we can distinguish several classes:

  * Punctuation: dots separate sentences,
    commas are variously used,
    and spacing separate elements;
  * Logical connectives: conjunction (``;``) and implication (``if: ... then: ...``),
    used to build rules, that are a class of complex element;
  * Variables, again used in the construction of rules;
  * Relations, mainly set and arithmetic relations;
    the set relations used to define non-logical elements,
    and the arithmetic relations as conditions in rules;
  * Operators, mainly the predicate operator ``[ ... ]``,
    which we will shortly touch again, and
    arithmetic operators;
  * Individuals: the numbers, and a few others
    like ``noun``, ``verb``,  ``thing``, ``exists``, ``number``.

This is not an exhaustive listing of predefined atomic elements,
for example we have not mentioned any time (or string?) related element.
It is, however, an exhaustive list of classes of atomic elements.

The non-logical elements are all of the "individual" class,
which we will in general call "terms".
The main atribute of terms is that
they can match variables in rules.

We can distinguish a few types of terms.
The first type are the metanouns: ``noun``, ``verb``, and ``number``.
Then we have the verbs: ``exists`` and any number of non-logical verbs derived from it;
the nouns: ``thing``, and any number of non-logical nouns derived from it;
the proper names, all non-logical;
and finally we have the numbers.

Complex elements
~~~~~~~~~~~~~~~~

There are a few classes of complex elements available
to give shape to the knowledge in the kb.

Definitions
###########

First, we have constructs that allow us to define
new non-logical terms.
There are three types of term that we can define:
verbs, nouns, and names. For each type we have
a different construct.
An example might be ``john isa person``,
where we define a term ``john`` of type name,
and assert (through ``isa``)
that it "belongs to" the term ``person``,
of type noun.

Predicates
##########

Then we have predicate elements.
Predicates are composed
with the predicate operator ``[]``,
enclosing a verb and 0 or more objects.
These objects can be terms of any type.
Predicates are themselves terms,
and can thus match variables in rules,
or appear as objects in other predicates.
In the example in the `home page <index>`_,
a predicate would be ``loves sue``.
This would be missing the ``[]`` operator,
that is needed when predicates are used as objects in other predicates;
so a more correct form would be ``[loves sue]``.
But this is not yet totally correct syntax.
We can have more than one object in a predicate,
and, in different facts,
the same verb can form predicates
with a different number of objects.
Thus, we need to label the objects.
To see this, consider that we have
a "goes" verb that takes 2 objects to form a predicate,
a "from" object and a "towards" object.
And consider that we want to use this verb
to form a fact where we only talk about
where someone goes,
and not where she comes from.
Using "[goes madrid]" would not tell us
whether "madrid" is a "towards" or a "from" object.
So, the syntactically correct form would be
``[loves who sue]``.
Obviously, we would have to incorporate the ``who`` label
in the definition of the ``loves`` verb,
and in the rule.

Facts
#####

Then we have fact elements.
Facts are composed of a subject, a predicate, and an optional time.
The subject can be any atomic term,
and the predicate has to be a predicate term.
We will leave time out of this introduction.
In the example in the `home page <index>`_,
a fact would be ``mike loves sue``.
Incorporating the corrections
explained for predicates,
we would have ``mike [loves who sue]``.

Rules
#####

Then we have rule elements.
Rules are composed of a set of
complex elements that we call the conditions,
and another set of complex elements, the consecuences.
Conditions and consecuences can contain variables,
and all variables in the consecuences must appear in the conditions.
They can be definitions, facts, arithmetic conditions,
or several other special constructs.
When we extend the kb,
asserted facts match conditions in rules,
and when all conditions in a rule are satisfied,
its consecuences are asserted.

Questions
#########

Then we have questions,
that are basically correspond to standalone rule conditions,
and that return the sentences in the kb that match them.

Orders
######

Finally, we have orders, that do not have any common form.
An example of an order is ``extend``, that extends the kb.

Language reference
------------------

Next I'm going to describe the **npl** language
in a bit more detail
going through its BNF grammar.
If in the previous introduction I have gone from bottom to top element,
here I will go
top down. To illustrate the different constructs of the language, I will
be refering to pieces from
`these tests <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/>`_.

.. toctree::
   :maxdepth: 3

   bnf
   npl_time
   npl_arith
   npl_negation
   npl_questions
   npl_macros




..
    Semantics
    ~~~~~~~~~
    
    The semantics of **npl** is two layered.
    Whereas the set relations are used to represent properties of the terms,
    facts are used to represent properties of the objects represented by the terms.
    I will try to show this with an example.
    Let's imagine we are building a web based software project management system.
    We develop the business logic with **npl**,
    and the web application with Python.
    We have an http server that gives requests
    to our web application,
    that dispatches them to view functions,
    that build the responses.
    To build the responses,
    the views can interface with **npl**.
    So the views want to be able to ask and tell things like:
    "This developer wants to push this patch on this repo, what is his clearance?
    Does he need review?"
    "This person wants to deploy this tag of this repo to this server,
    any critical service there that constrains what can be installed
    and that depends on that repo?"
    "this tester has confirmed this bug in this service."
    According to the responses obtained from **npl**,
    the views will produce one or another response,
    and will have these or those side effects.
    So this is one semantic level of **npl**:
    Whatever meaning the user may want to give it.
    Its intended meaning.
    
    However, only a part of what is expressed in **npl**
    is interpreted in the intended meaning of the program.
    In particular, only proper names and predicates, forming facts,
    would be used in these views.
    Most of the ontological work of developing the **npl** program
    is useless in views.
    If user "bob" requests a page that corresponds to the details
    of the document with id "doc1",



