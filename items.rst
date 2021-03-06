=======
 Items
=======

An item is a persisted object with one or more attributes that make up a
schema.

Attributes
==========

Axiom comes with the usual suspects of attributes:

- ``text`` for (Unicode) text
- ``bytes`` for bytes
- ``integer`` for integer values
- ``pointXdecimal`` (X varying from 1 to 10) for decimals of varying precision
- ``ieee754_double`` for floating point values
- ``timestamp`` for absolute points in time

It also comes with a few slightly more advanced ones:

- ``reference`` which allows you to store a reference to any other Axiom item
  (not just those of a particular type)
- ``textlist`` which allows you to store lists of (Unicode) text in one field
- ``path`` which allows you to store both relative and absolute paths
- ``inmemory`` which allows you to store non-persisted/non-persistable state
  on the item instance

All of these will be covered throughout the book.

Creating items and accessing attributes
=======================================

Creating an item class is done by subclassing ``axiom.item.Item``, and
assigning at least one attribute from ``axiom.attributes`` to a name in the
class body:

.. literalinclude:: person.py
   :language: python

.. testsetup::

    from person import Person

You can then create instances of that item by calling the ``Item`` class and
specifying the attributes as keyword arguments. You can access the attributes
on the item just like regular attributes:

.. doctest::

    >>> alice = Person(name=u"Alice")
    >>> assert alice.name == u"Alice"

Static, strong typing
=====================

Axiom item attributes are not just statically typed but also strongly typed.
For example, you can't assign a bytestring to a ``text`` attribute:

.. doctest::

    >>> Person(name="a bytestring")
    Traceback (most recent call last):
       ...
    ConstraintError: attribute [Person.name = text()] must be (unicode string without NULL bytes); not 'str'
    >>> alice = Person(name=u"Alice")
    >>> alice.name = "another bytestring"
    Traceback (most recent call last):
       ...
    ConstraintError: attribute [Person.name = text()] must be (unicode string without NULL bytes); not 'str'

Defaults and unspecified values
===============================

You're allowed to not specify an attribute, which sets it to ``None``:

.. doctest::

    >>> anonymous = Person()
    >>> assert anonymous.name is None

If you don't want to allow ``None`` as a value, set ``allowNone=False`` on the
attribute:

.. literalinclude:: coin.py
    :language: python

.. testsetup::

    from coin import Coin
    import decimal

.. doctest::

    >>> Coin()
    Traceback (most recent call last):
       ...
    TypeError: attribute [Coin.value = money()] must not be None
    >>> quarter = Coin(value=decimal.Decimal("0.25"))

You can also have default values. For example, we could have a petting zoo
with bunnies, and bunnies start out having been petted zero times:

.. literalinclude:: bunny.py
    :language: python

.. testsetup::

    from bunny import Bunny

.. doctest::

    >>> thumper = Bunny()
    >>> assert thumper.timesPetted == 0  # Aww :-(
    >>> thumper.timesPetted += 1
    >>> assert thumper.timesPetted == 1  # Yay :-)

Sometimes a default value isn't enough, and you need a default value factory
that gets called when the item gets created. Let's recite the alphabet:

.. literalinclude:: alphabet.py
    :language: python

.. testsetup::

    from alphabet import Letter

.. doctest::

    >>> a, b, c = Letter(), Letter(), Letter()
    >>> assert a.value == "a"
    >>> assert b.value == "b"
    >>> assert c.value == "c"
