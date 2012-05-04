
Tutorial: A content management system
=====================================

In this section we will try to develop a not so simple ontology that might be used as the metadata backend for a content management system (CMS). Let us imagine that such a CMS would basically consist of the following parts:

 #. A web application: mainly an HTTP request dispatcher and a system of views that the dispatcher calls to get responses for the requests;
 #. A database where actual content (text, images, etc.) is stored;
 #. A user authentication backend;
 #. The metadata backend that the views consult to produce responses.

The metadata backend would therefore know about content types, users, permissions, workflows, etc., and is the only part we will develop here.

Brief introduction to the anatomy of sentences in npl
-----------------------------------------------------

There are 4 basic kinds of sentences in npl. The first kind (noun definitions) have the form "<noun1> are <noun2>", and are used to define nouns in terms of other nouns. Nouns are akin to classes, where proper names would be the elements of those classes. Defining a noun in that way would mean that all individuals (proper names) of noun1 are also of noun2. If we enter ``dog are animal``, it will mean that all dogs are animals.

The second kind of sentence (name definitions) have the form "<name> isa <noun>", and are used to define proper names in terms of nouns.
If we enter ``bruto isa dog``, it will mean that Bruto is a dog (and, from the previous paragraph, that Bruto is an animal).

The third kind have the form "<subject> [<verb> <label> <object>(, <label2> <object2> ...)] <time>", and are used to assert facts. They have a subject, that can be any kind of term, and a predicate, enclosed in square brackets. The predicate is itself composed of a verb and any number of objects (or modifiers). The modifiers are composed of a label and another term, of any kind. Finally, we have a term of type time.

The fourth kind have the form "a <term> can <verb> <label> a <term>(, <label2> a <term2> ...)", and are used to define verbs in terms of the kind of subject and modifiers that they can take on facts.

For more details, see the `language reference <bnf>`_.

CMS
---

So let's start our CMS logic by defining a ``person`` noun::

  person are thing.

Now we can define ``person`` individuals, identified by proper names of ``person``. For example, we can name a ``person`` ``john``::

  john isa person.

We also need a "content" class of things. ``content`` will be the noun of all content objects, and the various content types (e.g., document) will be nouns derived from ``content``::

  content are thing.
  document are content.
  image are content.

Thus we can have content individuals::

  img1 isa image.

And now, we will define verbs that refer to the different actions that people can perform with content objects (or individuals). First, we define an abstract ``action`` verb that will be the ancestor of any other action::

  a person can action what a content.

The operational semantics of this would be that ``action`` is defined as a verb, that when used in a fact will require a ``person`` individual as subject of the fact, and that in such a fact, it may have an object or modifier (labelled ``what``) of type ``content`` (i.e., a content object).

We can now define more verbs derived from ``action``, that inherit its (just one) modifiers::

  a person can view (action).
  a person can edit (action).

Another verb that affects people that we are going to define is ``wants``. Whenever a user attempts to perform an action on the system (calls a URL mapped to a document object, clicks an edit button, etc.), we will tell the metadata backend (npl) that the user wants to perform the action. A basic pattern for the metadata rules will be "if someone wants to do such thing, and this and that conditions are met, (s)he does such thing". Therefore, a basic pattern for the views will be to tell nl that a user wants to do something, extend the knowledge base, and then ask whether the user does it.::

  a person can wants what a exists.

By using ``exists`` as the type of modifier, we are saying that the modifiers of ``wants`` must be predicates.

Now we define a fairly general verb, ``has``, that can have anything as subject and can have two modifiers, ``what``, that can be any thing, and ``where``, that has to be a context::

  context are thing.

  a thing can has what a thing, where a context.

We will use ``has`` for various things. For example, to tell the system that a certain user has a certain role in some context, or that a certain role has a certain permission, or that a certain content object has some workflow state.

Next, let's define 2 nouns: ``role`` and ``permission``. As we have hinted above, roles are related with permissions through the verb ``has``::

  permission are thing.
  role are thing.

We could now define a few more proper names for our ontology::

  member isa role.
  editor isa role.
  manager isa role.

  basic_context isa context.

  view_perm isa permission.
  edit_perm isa permission.
  manage_perm isa permission.

  member [has what view_perm] onwards.
  editor [has what view_perm] onwards.
  editor [has what edit_perm] onwards.
  manager [has what view_perm] onwards.
  manager [has what edit_perm] onwards.
  manager [has what manage_perm] onwards.

We can now assert that, for whichever context, the admin person has the manager role::

  admin isa person.

  if:
    Context1 isa context;
  then:
    admin [has what manager, where Context1] onwards.

We now define a verb, ``located``, that allows us to locate content objects in contexts::

  a content can located where a context.

Next, we define a ``status`` noun, that refers to the different workflow states that a content object can be in. As a starting point, we shall define 2 different states, public and private::

  status are thing.
  public isa status.
  private isa status.

We now define an abstract workflow action, that will be primitive to any workflow action::

  a person can wfaction (action).
  a person can publish (wfaction).
  a person can hide (wfaction).
  
Now we define a ``required`` verb, that is used to state that a certain permission is required to perform a given action over any content that is in a certain workflow state. Note that in this case, we are using an actual verb, and not a predicate, as the modifier for the ``required`` verb: We define it with a ``verb`` modifier. For the moment, we can not set bounds to the possible verbs that can be used as modifiers for these verbs: we use ``verb``, that is the only class we have for verbs::

  a permision can required to a verb, over a status.

At this point, we can define a rule that, when someone wants to perform an action over some content, decides whether (s)he is allowed to perform it or not, according to her roles and to the workflow state of that content. We want to assert that, if someone wants to perform some action on some content, and that content has some state and is located in some context, and the person has some role in that context that has the required permission to perform that action over that workflow state, then (s)he performs it::

  if:
    Person1 [wants to [ActionVerb1 what Content1]] at I1;
    Permission1 [required to ActionVerb1, over Status1] D1;
    Content1 [has what Status1] D2;
    Content1 [located where Context1] D3;
    Person1 [has what Role1, where Context1] D4;
    Role1 [has what Permission1] D5;
    I1 during D1, D2, D3, D4, D5;
  then:
    Person1 [ActionVerb1 what Content1] at I1.

Note the use of the ``ActionVerb1`` verb variable to range over actual ``action`` verbs.

We can now protect some actions with permissions::

  view_perm [required to view, over public] onwards.
  edit_perm [required to edit, over public] onwards.
  manage_perm [required to hide, over public] onwards.
  manage_perm [required to view, over private] onwards.
  manage_perm [required to edit, over private] onwards.
  manage_perm [required to publish, over private] onwards.

Next, we are going to give meaning to workflow actions. For that, we are going to define a ``workflow`` noun, an ``assigned`` verb that will relate workflows to content types (depending on the context the content object is in), and another verb ``has_transition`` that relates a workflow with an initial and a final workflow state and the workflow action that performs the transition::

  workflow are thing.

  a workflow can assigned to a noun, where a context.
  a workflow can has_transition start a status, end a status, by a verb.

With these terms in place, we can add a rule that states that, if some person performs some workflow action on some content, and that content is in the initial state of the transition corresponding to that action, and that action embodies the transition of some workflow that is assigned to the content type of the content object in the context in which the object is located, then the object ceases to be in the initial state and starts being in the final state of the transition::

  if:
    Person1 [Wfaction1 what Content1] at I1;
    Workflow1 [has_transition start Status1, end Status2, by Wfaction1] D1;
    Workflow1 [assigned to ContentNoun1, where Context1] D2;
    Content1(ContentNoun1) [located where Context1] D3;
    Content1 [has what Status1] D4;
    I1 during D1, D2, D3, D4;
  then:
    Content1 [has what Status2] until D1, D2, D3;
    finish D4.

So, let's provide a workflow for ``document`` and assign it to ``document`` in the basic context, and a couple of transitions for that workflow::

  doc_workflow isa workflow.

  doc_workflow [has_transition start private, end public, by publish] onwards.
  doc_workflow [has_transition start public, end private, by hide] onwards.

  doc_workflow [assigned to document, where basic_context] onwards.

With all this, we can start adding people and content objects, and test our ontology so far.


So, let's star using this ontology. We are going to define 2 contexts, 2 documents, one located in each context, both with an initial state private, and two people, each with the manager and editor role in opposite contexts::

  john isa person.
  mary isa person.

  context_of_john isa context.
  context_of_mary isa context.

  doc_of_john isa document.
  doc_of_mary isa document.

Let's start time::

  now.

  john [has what manager, where context_of_john] onwards.
  john [has what editor, where context_of_mary] onwards.
  mary [has what editor, where context_of_john] onwards.
  mary [has what manager, where context_of_mary] onwards.
  doc_of_john [located where context_of_john] onwards.
  doc_of_john [has what private] onwards.
  doc_of_mary [located where context_of_mary] onwards.
  doc_of_mary [has what private] onwards.

We extend the knowledge base::

  extend.

And now we can see that Mary cannot view or edit John's document, but john can::

  mary [wants what [view what doc_of_john]] now.
  mary [wants what [edit what doc_of_john]] now.
  john [wants what [view what doc_of_john]] now.
  john [wants what [edit what doc_of_john]] now.

  extend.

  mary [view what doc_of_john] now?
  False

  mary [edit what doc_of_john] now?
  False

  john [view what doc_of_john] now?
  True

  john [edit what doc_of_john] now?
  True

Time passes::

  now.

Mary cannot publish John's doc, but John can::

  mary [wants what [publish what doc_of_john]] now.
  john [wants what [publish what doc_of_john]] now.

  extend.

  mary [publish what doc_of_john] now?
  False

  john [publish what doc_of_john] now?
  True

And, now, john's document is in the public state, and so, Mary can view it, but Mary's is private and John cannot view it::

  doc_of_john [has what public] now?
  True

  mary [wants what [view what doc_of_john]] now.
  john [wants what [view what doc_of_mary]] now.

  extend.

  mary [view what doc_of_john] now?
  True

  john [view what doc_of_mary] now?
  False

Etc. etc.
