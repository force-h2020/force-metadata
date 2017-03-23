OWL specification of the ontology
---------------------------------

**Version**: 1.0 

FORCE will require an ontology of the interesting objects. This ontology forms
the basis of the data model we use to read data. This document presents
direction of how things should be expressed. 
We will in particular consider:

- Use of Protege as the editor of the ontology
- Migration of SimPhoNy ontology (currently in a YAML custom format)
- Extensions for those concepts OWL does not allow to express.

Protege
-------

Protege is the graphical tool we use to handle the ontology. Downloadable for free from
http://protege.stanford.edu/. It allows us to

- create new classes in the appropriate taxonomy
- create new properties referring to either other classes (object properties) or 
  literal data (data properties)
- set constraints to the data

SimPhoNy ontology
-----------------

SimPhoNy ontology uses a custom YAML format to express what is a taxonomy of
classes representing concepts.  This taxonomy encompasses various concepts in
chemistry and physics, and makes it clear their meaning and relationship to
other concepts. The hierarchy of classes defines "is-a" relationships among
concepts.

Specifications
--------------

Fundation
'''''''''

The Ontology MUST define the following.

Datatypes:

- CUBAKey (parent of xsd:string)

Annotation properties:

- cubaKey (range: CUBAKey)
- default (range: any)

Adding a new datatype
'''''''''''''''''''''

- A new datatype MUST be a child (direct or indirect) of the appropriate type (e.g. ``Mass`` must be derived from ``xsd:float``).
- the datatype name MUST be CapitalisedCamelCase. (e.g. ``ZetaPotential``)
- the datatype SHOULD have an annotation ``cubaKey`` whose value is the name of the datatyp, fully capitalized (e.g. ``ZETA_POTENTIAL``).
  If not present, it is assumed to be automatically created from the dataype name with an appropriate algorithm.
- a Datatype Property MUST be defined with the following requirements:
  - the name equal to the datatype name, in lowercase camelcase (e.g. ``zetaPotential``)
  - Range set to the datatype.


Adding a new class
''''''''''''''''''

- A new class MUST be a child (direct or indirect) of ``CUDSItem``.
- the class name MUST be CapitalisedCamelCase. (e.g. ``PressureGradient``)
- the class SHOULD have an annotation ``cubaKey`` whose value is the name of the class, fully capitalized (e.g. ``PRESSURE_GRADIENT``).
  If not present, it is assumed to be automatically created from the class name with an appropriate algorithm.
- an Object Property MUST be defined with the following requirements:
  - the name equal to the class name, in lowercase camelcase (e.g. ``pressureGradient``)
  - Range set to the new class.

Adding properties to a class
''''''''''''''''''''''''''''

To add a property to a class:

- For a class to have a property, an appropriate restriction on the class MUST be added. 
- If the property is a data property, an annotation ``default`` MAY be present, indicating the appropriate
  default value. The default MUST have the appropriate type for its destination.
- If the property is an object property, an annotation ``default`` MAY be present, indicating the class
  of the default value. The default MUST have the appropriate type for its destination.
