language essentials
===================

Sentences
----------

.. productionlist::
    sentence : `statement`
             : | `question`
             : | `order`

Sentences are the top level grammatical elements. These are the elements that
can be entered (told or asked) into npl's knowledge base (kb). There are 3
kinds of sentences: statements, questions, and orders. Statements
add to the information held in the kb, and questions query the kb. Orders
do different things to the kb.

Order: extend
-------------

.. productionlist::
    order : `EXTEND` `DOT`
    EXTEND : "extend"
    DOT : "."

If you issue the order ``extend.``, the kb will be extended to all its
logical consecuences.

Order: import
-------------

.. productionlist::
    order : `IMPORT` `URI` `DOT`
    IMPORT : "import"
    URI : '"' <An URI> '"'

If you issue the order ``import "http://example.com/ont.npl".``, the kb will
download the file at the URI and load it into the kb.

Statements
----------

.. productionlist::
    statement : `definition` `DOT`
              : | `fact` `DOT`
              : | `rule` `DOT`

Statements are asserted: they have truth value.
There are 3 main forms of statements, all ended with a dot, which we will
call definitions, facts, and rules.
Definitions are used to define terms. Facts use those terms to establish
relations among them. And rules establish relations (logical relations,
basically implication) among the relations expressed in the facts.

Definition of terms
-------------------

.. productionlist::
    definition : `noun_def`
               : | `name_def`
               : | `verb_def`

There are 3 types of terms we can define. We will call them (proper) names,
nouns, and verbs.

Noun definitions
~~~~~~~~~~~~~~~~

.. productionlist::
    noun_def : `TERM` `ARE` `TERM`
    ARE : "are"
    TERM : "[a-z][a-z_]*\d*"

We define a new noun by relating it with another (already defined) noun through
the reserved word ``are``. To get started, we use a primitive predefined noun,
``thing``. An example of a noun definition is ``person are thing.``.

The symbols for atomic terms in general must start with
a lower case letter followed by
any number of lower case letters or underscores,
and 2 underscores in a row are forbidden.

For more examples, you can look at
`lines 20-24 in this test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L20>`_.

Name definitions
~~~~~~~~~~~~~~~~

.. productionlist::
    name_def : `TERM` `ISA` `TERM`
    ISA : "isa"

Proper names are defined relating them with a noun through the reserved word
``isa``. An example is ``john isa person.``.

Name terms are special among the rest of terms in that they may end in any number of digits.
The rest of terms must be terminated with an lower case letter.

For more examples, you can look at
`lines 26-35 in the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L26>`_.

Types of terms
~~~~~~~~~~~~~~

Names and nouns establish a class structure. The relation established by ``are``
among 2 nouns has the same form as the subclass relation among 2 classes, and
the relation established by ``isa`` among a name and a noun has the same form as
the relation between an individual and a class it belongs to. So, for example,
the mentioned definitions in the
`the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L20>`_
entail that ``document are thing``, or that
``mary isa thing``. Also, this means that if we ask the system for a ``thing``, ``mary``
will be retrieved, and if in a rule we require a ``thing``, ``mary`` will match.

This class structure is explicit in the case of nouns and names, and is
(implicitly) pervasive among the rest of terms in **npl**. So, for
example, all predicates
(predicates are complex terms composed of a verb and any number of objects,
as we shall see below) are implicitly related by ``isa`` with their verbs.
In addition, all verbs are to be thought of as related through ``isa`` with the
predefined term ``verb``, and all nouns with ``noun``, all numbers with
``number``, and all times with ``time``.

This allows us to talk about types of terms. A type of terms is a term, and
the terms that are of that type are the terms related with the type term
through ``isa``. Therefore, we have six mayor types of term:
``noun`` (what we called "nouns" when introducing the `basic programming elements <npl_ref#basic-programming-elements>`_),
``verb`` (the verbs),
``thing``(the proper names),
``exists`` (which is the primitive predifined verb, and as type corresponds to the predicates),
``number`` (the numbers), and ``time``,
and any number of subtypes of ``thing``
(i.e., the programmer defined nouns)
and ``exists`` (the programmer defined verbs).
Metanouns would correspond to the hidden term ``word``.

For example, ``doc1`` is a term of type ``thing`` (and also of type ``document``),
and ``document`` is a term of type ``noun``.

NOTE: since the definitions of verbs set bounds on the predicates and facts
where they can appear, we shall defer their introduction until we have
introduced predicates and facts.

Facts
-----

.. productionlist::
    fact : `subject` `predicate`
    subject : `TERM`

Facts are composed of a subject and a predicate. The subject is
a name, a noun or a verb.

.. productionlist::
    predicate : `LBRACK` `verb` `modification` `RBRACK`
              : | `LBRACK` `verb` `RBRACK`
    verb : `TERM`
    LBRACK : "["
    RBRACK : "]"

The predicate is a complex term enclosed in square brackets, composed of a verb
and an (optional) modification.

.. productionlist::
    modification : `object` `COMMA` `modification`
                 : | `object`
    COMMA : ","

A modification is one or more objects, separated by commas.

.. productionlist::
    object : `LABEL` `object`
    object : `TERM`
           : | `predicate`
    LABEL : <same pattern as TERM>

A object is composed of a label and an object, that can be any kind of
(atomic or complex) term except a time: a noun, a verb, a name, a number, or a
predicate.

A simple example of a fact could be ``john [view what img1]``, where ``john``
is the subject and ``[view what img1]`` the predicate, where ``view`` is the
verb, and ``img1`` is a object with label ``what``.

Definition of verbs
-------------------

.. productionlist::
    verb_def : `A` `TERM` `CAN` `TERM` `LPAREN` `verbs` `RPAREN` `modification_def`
             : | `A` `TERM` `CAN` `TERM` `modification_def`
             : | `A` `TERM` `CAN` `TERM` `LPAREN` `verbs` `RPAREN`
    verbs : `verb` `COMMA` `verbs`
          : | `verb`
    CAN : "can"
    A : "a"

In the definition of a verb (with name given as the second TERM in the
verb_def) we can specify 3 different things. First, the type of
term that can act as subject in a fact where the new verb forms the predicate
(given by the first TERM in the definition); second, the
(already defined) verb(s) from which we derive the new verb (given in the
verbs part of the definition); and third, the objects that the verb can take
to form the predicate (the modification_def). Both the verbs part or the
modification_def part can be omitted. Omitting the verbs, we assume its
parent to be ``exists``; omittin the modification_def, the verb will
inherit those of its parents.

.. productionlist::
    modification_def : `mod_def` `COMMA` `modification_def`
                     : | `mod_def`
    mod_def : `LABEL` `A` `TERM`

The objects that a verb can take are specified through mod_defs, where we
give the label that the object will take, connected through the reserved word
``a`` with the type of terms that can be used as that object.

So, for
example, in
`lines 37-40 in the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L37>`_,
we define verbs that express actions that a person can perform on
content. For this we use the primitive predefined verb
we mentioned earlier: ``exists``.

Derived verbs inherit the mod_defs that they do not override.
Therefore, we do not need to specify a mod_def for a child verb if it
coincides with one of its parents.

With these verbs, we can state facts such as ``pete [owns what doc1].``
or ``sue [edit what img2].``

Rules
-----

.. productionlist::
    rule : `IF` `COLON` `conditions` `SEMICOLON` `THEN` `COLON` `consecuences`
    conditions : `conditions` `SEMICOLON` `condition`
               : | `condition`
    condition : `fact`
              : | `name_def`
    consecuences : `consecuences` `SEMICOLON` `consecuence`
                 : | `consecuence`
    consecuence : `fact`
    IF : "if"
    COLON : ":"
    SEMICOLON : ";"
    THEN : "then"

A rule consists of 2 sets of statements, the conditions and the consecuences.
Conditions and consecuences are, mainly, facts (though they can be other types
of statements, as we shall be seeing below). Atomic facts (facts that are
asserted on their own, outside of rules) can match the conditions of rules,
and, when all conditions in a rule are matched, its consecuences are
(atomically) added to the kb when we issue an ``extend.`` order.

An atomic fact matches a condition in a rule if (but not only if) they are
identical (ignoring the order of objects in the predicate). It also matches
when they are identical except that the atomic fact specifies more objects
than the condition.
Also, the order of the objects is immaterial for matching.

We can use logical variables in place of terms in the conditions and
consecuences of a rule. A logical variable is a symbol that starts with a
capital letter, followed by any number of lower case letters, digits,
and underscores,
and ends with any number of digits. For example, ``Person1``.
A logical variable has a range, that is a
type of terms. The range of a variable can be obtained by lower casing its
first letter and removing its final digits. A fact will match the condition of
a rule if they are identical except that, where the condition has a variable,
the fact has a term
that is in the range of the variable. The scope of variables is the rule: if a
term matches a variable, it does so for all its occurrences within the rule.

For a first example, we need to add a couple more of BNF rules:

.. productionlist::
    subject : `VAR`
    object : `VAR`
    VAR : "([A-Z][a-z_]*)(Verb|Noun|Word)?\d+"

So, for example, in
`line 42 in the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L42>`_
we define a verb ``located``, which we use in a rule in
`line 44 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L44>`_.

With this rule, and the facts in
`lines 51 and 52 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L51>`_,
the system will conclude that ``doc1 [located where ctx2]``.

Although we are seeing different types
of variables corresponding to the different types of terms,
under the hood there is really just one type of variable.
The different forms of variables is just syntactic sugar
for constraining the variable.
For example, ``Person1``
would correspond to something like "X1 where X1 isa person".

Predicate variables
-------------------

.. productionlist::
    predicate : `LBRACK` `VAR` `RBRACK`

We have mentioned that we can use predicates as objects in the objects of other
predicates. This means that, in rules, we must be able to use variables that
range over predicates. We do this by building a variable from a verb, and
enclosing it in square brackets. For example, from ``locate``, we might have
``[Locate1]`` (the brackets are not part of the variable, but mark it as a
predicate).

To provide a working example, we define a couple of verbs that take a
predicate as object, in
`lines 60 and 61 in the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L60>`_,
and build a rule with them in
`line 63 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L63>`_.

With this rule, and the facts in
`lines 70-71 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L70>`_,
the system will conclude that ``sue [view what doc1]``.

Verb variables
--------------

.. productionlist::
    predicate : `LBRACK` `VAR` `VAR` `RBRACK`
              : | `LBRACK` `VAR` `modification` `RBRACK`

Since we can have verbs as subject or object in facts, we need to be able to
use variables in rules that range over verbs. We do this by capitalizing the
name of a verb, and appending to it "Verb" and an integer. for example, a verb
variable made from ``locate`` would be ``LocateVerb1``. To show a more complete
example of this, we define a verb ``may`` in
`line 79 in the test program <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L79>`_,
that will take a verb as object, and a rule that uses ``may`` in
`line 84 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L84>`_.
Now, if we add the facts in
`lines 92, 93 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L92>`_,
the system will conclude that ``mary [view what doc1]``.

So, as seen in
`line 85 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L85>`_,
we can use a verb variable in a predicate with objects. Also without
objects, just by itself in the predicate, like ``[Content_actionVerb1]``. This
stands for a predicate where the content_action verb is alone without
objects, as opposed to ``[Content_action1]`` where nothing is said of the
number of objects.

If, in the rule in
`line 84 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L84>`_,
we had not wanted to relate the context in which the content
is located with the context in which the person is allowed to do the content action,
we might have said::

    if:
        Person1 [wants that Person1, do [Content_actionVerb1 Content_action1]];
        Person1 [may what Content_actionVerb1];
    then:
        Person1 [Content_action1].


Let's take a look at the construct ``[Content_actionVerb1 Content_action1]``.
It stands for a predicate, and any predicate matching it would also match
``[Content_action1]``. However, we want to specify that the matching predicate's
verb must be the one that matches the variable ``Content_actionVerb1`` in the
second condition. Thus the oddly redundant form.


Noun variables
--------------

.. productionlist::
    subject : `varvar`
    object : `varvar`
    varvar :  `VAR` `LPAREN` `VAR` `RPAREN`
    LPAREN : "("
    RPAREN : ")"

The same we have said about verb variables can be said of noun variables.
The only difference is when, in a condition, we want a variable form to range
over names that have a type given by another (noun) variable. In that case, we
give the name variable inmediately followed by the noun variable enclosed in
parentheses. For example, ``Person1(PersonNoun1)``.

In the rule in
`line 151 <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/cms.npl#L151>`_
there is an example of the use of noun variables.
