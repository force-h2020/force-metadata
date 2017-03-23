API for OWL patching
--------------------

The following specs describe a simple high level python API to patch the OWL ontology.
It is provided to:

- simplify access to our users so that they can add new objects to the 
- assist in migration from data with an old API to data with a new API


Creation of new datatype
''''''''''''''''''''''''

You can define a new datatype as follows::

    class MyDataType(force_owl.Datatype):
        parent="xsd:integer"

if parent is not specified, the datatype will be parentless in the OWL hierarchy

Creation of new class
'''''''''''''''''''''

To define a new class, you have to specify the following::

    class MyClass(force_owl.Class):
        parent="ParentClass",
        properties=[
            Property(MyDataType, default=1)
            Property("MyOtherClass")
            ]
        )

The following applies apply:

- ``parent`` MUST be present, and refer to a class or class name.
- ``Property`` can refer to a previously declared class/datatype, or to an existing class/datatype (by string).

