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

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
RFC 2119. [1]

Changelog
---------

- Initial release

Terminology
-----------

The following terms are used in the remainder of this document and are prescriptive:

    - ``(non-qualified) CUBA key``: a fully capitalized, underscored name used as a moniker to refer to a class
      or data type in our data model (e.g. COMPUTATIONAL_MODEL).
    - ``qualified CUBA key``: a CUBA key prefixed with the ``CUBA.`` string (e.g. CUBA.COMPUTATIONAL_MODEL)

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

The ontology is based on the following IRI namespace:

``http://purl.org/force-h2020/ontologies/force/v1#``

The customary prefix is ``force:`` and will be used for the rest of this document, where needed.

The Ontology MUST define the following Datatypes:

- ``CUBAKey``: defines a non-qualified CUBA Key. with the following format CUBA.
- ``doubleArray``: an array, matrix, or n-dimensional array of ``xsd:double`` values. 
    Its lexical representation is in python "list of lists" notation. For n-dimensional
    arrays, the assumed order is python/C-like (row-wise)
- ``intArray``: as above, but values are ``xsd:integer``.
- ``shapeArray``: as above, but values are represented as a list of ``xsd:integer`` or the literal value ``None``
   to indicate shape dimensions that are open ended. For example, a 3D geometry is indicated as ``[3, None]`` or ``[None, 3]``
   depending on the chosen convention for the orientation of the information. 
- ``stringArray``: as above, but values ``are xsd:string``.

The following annotation properties MUST also be defined:

- ``cubaKey (range: CUBAKey)``: used to annotate the CUBA key of a given class.
- ``default (range: any)``: used to define a default value for a given relationship if not specified. 
   This addresses a shortcoming of OWL not being able to express this concept.
- ``shape (range: shapeArray)``: describes the shape of the array.

Adding a new CUDS Item
''''''''''''''''''''''

- A new class MUST be created with the following requirements:
    - MUST be a child (direct or indirect) of ``CUDSItem``
    - the class name MUST be CapitalisedCamelCase. (e.g. ``PressureGradient``)
    - the class MAY have an annotation ``cubaKey`` whose value is the name of the class, fully capitalized (e.g. ``PRESSURE_GRADIENT``).
      If not present, the associated value will be automatically devised from the class name with an appropriate conversion algorithm.
- An Object Property MUST be defined with the following requirements:
    - the name equal to the class name, in lowercase camelcase (e.g. ``pressureGradient``)
    - MUST be declared Functional.

Adding a new CUBA datatype 
''''''''''''''''''''''''''

- a Datatype Property MUST be defined with the following requirements:
    - the datatype property name MUST be lowercaseCamelCase. (e.g. ``zetaPotential``)
    - the datatype property MAY have an annotation ``cubaKey`` whose value is the name of the datatype, fully capitalized (e.g. ``ZETA_POTENTIAL``).
      If not present, it is assumed to be automatically created from the dataype property name with an appropriate algorithm.
    - Range set to the appropriate ontology datatype (e.g ``xsd:double`` for a CUBA datatype that is a double in nature).
    - MUST be declared Functional.
- If range is an array (``intArray``, ``stringArray``, ``doubleArray``) the annotation ``shape`` MUST be present, indicating the expected 
  shape of the datatype. 

Adding CUBA properties to a class
'''''''''''''''''''''''''''''''''

To add a datatype property to a class:

- An appropriate restriction on the class MUST be added.
  Example: to express that ``NoseHoover (class)`` class has property ``couplingTime (data property)``, the following restriction MUST be added::
        
         couplingTime exactly 1 xsd:double

  the restriction type MUST match the range of the datatype property.

- An annotation ``default`` MAY be present on the restriction, indicating the appropriate default value. 
  The default MUST have the appropriate type and shape for its destination.

To add a object property to a class:

- An appropriate restriction on the class MUST be added::
    
         material exactly 1 Material

- If the property is an object property, an annotation ``default`` MAY be present on the restriction, indicating the class
  of the default value. The default MUST have the appropriate type for its destination.


Modeling lists
''''''''''''''

OWL does not allow simple specification of ordered lists. When multiple entities are provided with the same statement, the items would
be unordered. Normally, a list of entities contains semantic information about the order. You have the following options:

1. for datatype properties, give semantic meaning to the positional information. Example: a Thermostat has two temperatures: 
   start and stop. Define:
   
   - datatype Property ``startTemperature`` derived from ``temperature``
   - datatype Property ``endTemperature`` derived from ``temperature``
   - restriction on the Thermostat class ``startTemperature exactly 1 xsd:double``, ``endTemperature exactly 1 xsd:double``

2. for datatype properties:
   - define an datatypeProperty with a pluralized name of the contained objects (e.g. ``temperature -> temperatures``)
   - add restriction on the Thermostat class against an array: ``temperatures exactly 1 arrayDouble``

3. for object properties, the following condition MUST be added:
 
   - define a class with a pluralized name of the contained objects (e.g. ``Material -> Materials``). The class MUST derive also from ``rdf:List``.
   - define on the ``Materials`` class the restrictions::

       rdf:first exactly 1 Material
       rdf:rest exactly 1 Materials

     Note that the ``rdf:first`` refers to the singular class, while ``rdf:rest`` refers to the plural (list) class.

   - define an objectProperty with a pluralized name of the contained objects (e.g. ``materials``)
   - define on the hosting class the restriction ``materials exactly 1 Materials``

   - a ``default`` annotation MAY be specified on the ``materials`` restriction. If specified it MUST contain python parseable code that perform the 
     initialization of a list of classes as from the ontology.



