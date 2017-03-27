API for OWL patching
--------------------

The following specs describe a simple high level python API to patch the OWL ontology.
It is provided to:

- simplify access to our users so that they can add new objects to the 
- assist in migration from data with an old API to data with a new API


Creation of new datatype
''''''''''''''''''''''''

You can define a new datatype as follows::

    from force import DataType

    class MyDataType(DataType):
        type=int

supported types:

- ``str``
- ``int``
- ``float``
- ``array(int)``
- ``array(float)``
- ``array(str)``

A shape can also be specified for arrays as ``array(int, [3, None])``, where
``None`` is intended as no limit. By default, ``array(int)`` is equivalent to 
``array(int, [None])`` (an unlimited, one-dimensional array)

Creation of new class
'''''''''''''''''''''

To define a new class, you have to specify the following::
    
    from force import Class, Property

    class MyClass(Class):
        parent="ParentClass",
        properties={
            Property(MyDataType, default=1)
            Property(MyOtherClass)
        }

The following applies apply:

- ``parent`` MUST be present, and refer to a class or class name.
- ``properties`` must be a set of Property objects.

