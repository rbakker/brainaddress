# Brain Addressing System manuscript
## Title
The Brain Addressing System: unequivocally addressing the brain in 40 characters.

## Abstract
Most types of data measured in the brain can only be meaningfully compared with other data if they are registered to a well defined reference space.
For each of the commonly studied species, there are a number of brain atlases that fulfill the role of reference space. The problem that arises in practice is that the reference space is often specified just by its name, while there are many essential details that need to be known, in particular units, anatomical orientation and origin. This is what the Brain Addressing System (BAS) aims to solve. It consists of a set of definition files maintained by BAS and its providers, in which resources like reference spaces and transformations are described. The structure of these files is strictly controlled by the BAS schema language. This language also defines a shorthand notation for each resource, which ultimately results in a shorthand brain address that is examplified here: 
`bas.brainaddress(sba.ABA_v3(PIR,um,corner),[0,1,2])`
The shorthand brain address can be used in file headers or even as part of file names to unequivocally address the brain.

## Introduction

The Brain Addressing System (BAS) solves a number of common issues with brain coordinates used by experimental data sets. While public data sets are often aligned to a 'well known' reference space, it is often not clear what 'incarnation' of the atlas is referred to. This is not only a versioning issue, but even basic properties as units, axes orientation and origin are often left unspecified. As a consequence, combining data sets from different sources can be a frustrating experience that requires a lot of trial and error. For this reason we developed BAS, in which an atlas is always referenced in conjunction with the chosen unit, orientation and origin. BAS works with providers, which are individuals or institutes who help to populate the brain atlasing resources exposed by BAS. The providers supply *atlas definition files* in which all possible versions of an atlas are captured in a set of well defined parameters.

## BAS definition files

The main output of the Brain Addressing System and its providers are the definition files that describe brain atlasing resources. These definition files are structured according to the BAS-Schema syntax, detailed in section <#basschema>. This syntax can be used to define both data structure and content. It uses concepts from Object Oriented Programming languages, and distinguishes classes and instances. Classes define the structure of a data resource in terms of its required and optional properties. Instances are classes in which all required properties have an assigned value. The role of the BAS core definition file is mostly to define classes such as atlases, transformations, landmarks etc., while the role of BAS providers is to define extensions and instances of these classes, such as the *Allen Brain Atlas version 3* (ABA_v3) atlas and the affine transformation between `Waxholm` space and `ABA_v3`.

BAS providers can also define new classes, like a class derived from `bas.location` that defines a curve as a location, rather than a single coordinate, or a class derived from `bas.transform` that replaces the standard affine transformation by a nonlinear deformation with a particular set of parameters.

In general, definition of *instances* is useful to feed existing software like the Scalable Brain Atlas with new atlases and transformations. The definition of new *classes* is useful when the provider maintains software that supports the properties introduced by the new classes.

## BAS-Schema language specification
<name=basschema>

The Brain Addressing System uses definition files that use *BAS-Schema syntax*. It is closely related to *JSON-Schema*, a well-supported and powerful language to specify the structure of data objects. Use of a standard schema language has the advantage that documentation and maintenance is already taken care of, and that software to validate objects is readily available.
The JSON-Schema language is less verbose than its XML-based counterpart *XML Schema Definition language*, and rather intuitive to write. However, some of its constructs, like references to other definition files and class inheritance mechanisms, are unnecessary complex for our use case. We therefore propose BAS-Schema, a simplified schema language. When necessary, BAS-Schema can be machine translated to JSON-Schema syntax.

The correspondence between BAS-Schema and JSON-Schema is that BAS-Schema supports all JSON-Schema constructs that do not involve special keys that start with a dollar sign.

The differences between BAS-Schema and JSON-Schema are:
- BAS-Schema uses YAML instead of JSON as the notation format. This just means that the user can be more sloppy with commas and quotes, since YAML is a superset of JSON that has more relaxed formatting rules. The user can validate the YAML syntax at an online YAML to JSON conversion service (https://nodeca.github.io/js-yaml/, https://yaml-online-parser.appspot.com/).
- JSON Schema uses a `definitions` section to define reusable types. A BAS-Schema document contains *only* that section, nothing else.
- Where JSON-Schema defines the structure of a document by specifying data *types*, BAS-Schema defines *classes* and *instances* instead. Classes are much like the JSON-Schema *types* but support inheritance through the new `extends` field. An *instance* is defined as a class for which all *required* properties have a value. These values are specified through the new `propertyValues` field, detailed in the <a href="#propertyValues">next section</a>. The distinction between classes and instances makes it possible to use BAS-Schema to describe both the data itself (instances), and its structure (classes).
- BAS-Schema does not use the JSON-Schema `required` field, but instead treats all properties that do not specify a `default` value as *required*.
- BAS-Schema defines a `modifiers` field for the purpose of defining a shorthand notation of class instances. In that shorthand notation, the properties listed in the `modifiers` field can be specified as a comma-separated list of values, as detailed in the <a href="#propertyValues">next section</a>. Modifier properties *must* specify a default value.
- In BAS-Schema, wherever the field `type` would be used in JSON-Schema, it can be replaced by the field `instanceOf`, with a value formatted as providerId.className.
- In BAS-Schema, all class names *must* be structured as `providerId.className`. The `providerId` plays the same role as a `namespace prefix` in XML. It refers to the BAS provider who defines the class. A provider must use its own id when creating a class, but can use classes from all other providers to extend or create instances of. The schema interpreter uses the `providerId` to locate the file in which the class is defined. Definition files of BAS itself use providerId 'bas'.

The following example illustrates many of the BAS-Schema concepts.
 
```yaml
# Example definition file of provider `sba`
{
  # Define the `citation` class with only one field: `doi`.
  sba.citation: {
    properties: {
      doi: {
        type: string
        default: ""
      }
    }
    modifiers: [doi] 
  }
  # Define the `volume` class, by extending the bas.volume
  # class (defined elsewhere) and adding a definingCitation
  # field that is an instance of the class sba.citation.
  sba.volume: {
    extends: bas.volume
    properties: {
      definingCitation: {
        instanceOf: sba.citation
      }
      location: string
    }
  }
}
```
## Specifying property values
<a name="propertyValues">
The new `propertyValues` field is used to specify values for properties of the parent class. If a property has a `type` of string, number, array or object, then this is straightforward. For fields that are an `instanceOf` some class, the value can be specified in two ways.
1. As an object with a single key, equal to providerId.classId, and value depending on the `type` of the class. Example:
```
propertyValues: {
  definingCitation: {
    sba.citation: { doi: "10.1007/s12021-014-9258-x" }
  }
}
```
2. As a *shorthand string*, as detailed in the <a href="#shorthand">below</a>. 

## Versioning of definition files

Each definition file carries a version number formatted as `major.minor.patch`, where only major version changes may cause incompatabilities with derived classes. Minor version changes may be due to newly added classes or newly added properties. Patches are bug-fixes or different descriptions of existing properties. The version number is indicated in definition files by defining a class `providerId.version` which extends the `bas.version` class, and lists version numbers for each of the provider definition files that it depends on. 

## BAS shorthand notation
<a name="shorthand">
This shorthand notation is available for classes that define the previously introduced `modifiers` field. The shorthand starts with the the providerId.className token, and then between brackets it lists values for the modifiers in the correct order, separated by comma's. The list may be truncated at the end, and values between commas may be omitted. A further compactness is achieved by leaving out the class name for classes that have `type` "string" or "number" or "array" *and* correspond exactly to the class referred to by the `instanceOf` field. This makes it possible for example to replace `bas.units(mm)` by just `mm` in a property of the `instanceOf: bas.units` type. The shorthand string is widely used to refer to brain atlases, such as `sba.ABA_v3(PIR,um,corner)` which modifies the `sba.ABA_v3` atlas to have *PIR* orientation, *um* units and the origin at the *corner* landmark. In a full brainaddress (space + location) the shorthand is used recursively, for example `bas.brainaddress(sba.ABA_v3(PIR,um,corner),[0,1,2])`. See the <a href="">bas definition file</a> to validate whether this is a valid shorthand.
```comment
to do: use bas.landmark.ac and guess the bas.landmark part
so that ac can be used as an identifier.
```

## Translation of BAS-Schema to JSON-Schema

The translation to regular JSON-Schema serves the purpose of being able to use regular JSON-Schema validation tools for BAS-Schema definition files.
For the translation, the following rules apply.
- the YAML is converted to JSON and parsed.
- the resulting tree is placed in a `definitions` field with the appropriate JSON-Schema context.
- `extends` is replaced by inserting all properties of the parent class in an 'addAll' construct, whereby a `{ $ref: link }` construct is used to link between documents.
- `propertyValues` are replaced by `const` properties.
- The `modifiers` list is kept for reference but has no meaning in JSON-Schema. For properties that reference an `instanceOf` class that has a `modifiers` list, the JSON-Schema type is specified as either 'string' (in case a shorthand string is used) or the type of the class, using the `anyOf` construct.
