npl
###

**npl** is a programming language for building `expert systems <http://en.wikipedia.org/wiki/Expert_systems>`_.
It is very simple, yet extremelly powerful. It is
distributed under the `GNU General Public License V3 <http://www.gnu.org/licenses/gpl.txt>`_.

Like other expert system languages,
**npl** deals with rules and facts.
Various facts can make a rule applicable.
An applicable rule is then asserted.
As an example,
a program that applies
Newton's third law of motion
to interpersonal relations::

  person are thing.
  mike isa person.
  sue isa person.
  loves isa verb.
  mike loves sue.
  if: Person1 Verb1 Person2;
  then: Person2 Verb1 Person1.

With this, **npl** would conclude that ``sue loves mike`` [#f1]_.

.. toctree::
   :hidden:

   npl_ref
   Tutorial <tut>
   npl_foundation
   npl_install
   npl_interfaces
   support

-----

.. rubric:: Footnotes

.. [#f1] For clarity, in these examples I have removed a little bit of necessary syntax, that is only needed to allow for more complex developments.
