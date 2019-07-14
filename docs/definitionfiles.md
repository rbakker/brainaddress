## BAS definition files

The main output of the Brain Addressing System and its providers are the definition files that describe brain atlasing resources.
These definition files are structured according to the <a href="basschema.md">BAS-Schema syntax</a>. This syntax can be used to define both data structure and content. The role of the BAS core definition file is to define classes such as atlases, transformations, landmarks etc., while the role of BAS providers is to define instances of these classes, such as the *Allen Brain Atlas version 3* (ABA_v3) atlas and the affine transformation between `Waxholm` space and `ABA_v3`.

BAS providers can also define new classes, like a class derived from `bas.location` that defines a curve as a location, rather than a single coordinate, or a class derived from `bas.transform` that replaces the standard affine transformation by a nonlinear deformation with a particular set of parameters.
In general, definition of *instances* is useful to feed existing software like the Scalable Brain Atlas with new atlases and transformations. The definition of new *classes* is useful when the provider also maintains software that supports the properties provided by the new classes.
