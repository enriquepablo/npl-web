Language reference
==================

**npl** is a logic programming language. In the use of **npl** there
are basically 4 stages:

 * Define terms;
 * Build statements and rules with those terms;
 * Extend the set of statements and rules to all logical consequences;
 * Query that extended set.

I'm going to describe the **npl** language going through its BNF grammar, from
top down. To illustrate the different constructs of the language, I will
be taking pieces from
`tests in <https://github.com/enriquepablo/nl/blob/master/nl/npl_tests/>`_.

.. toctree::
   :maxdepth: 3

   bnf
   npl_time
   npl_arith
   npl_negation
   npl_questions
   npl_macros
