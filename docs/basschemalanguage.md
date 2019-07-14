## BAS-Schema language specification

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
2. As a *shorthand string*. This method is only available for classes that define the previously introduced `modifiers` field. The shorthand starts with the the providerId.className token, and then between brackets it lists values for the modifiers in the correct order, separated by comma's. The list may be truncated at the end, and values between commas may be omitted. A further compactness is achieved by leaving out the class name for classes that have `type` "string" or "number" or "array" *and* correspond exactly to the class referred to by the `instanceOf` field. This makes it possible for example to replace `bas.units(mm)` by just `mm` in a property of the `instanceOf: bas.units` type. The shorthand string is widely used to refer to brain atlases, such as `sba.ABA_v3(PIR,um,corner)` which modifies the `sba.ABA_v3` atlas to have *PIR* orientation, *um* units and the origin at the *corner* landmark. In a full brainaddress (space + location) the shorthand is used recursively, for example `bas.brainaddress(sba.ABA_v3(PIR,um,corner),[0,1,2])`. See the <a href="">bas definition file</a> to validate whether this is a valid shorthand.

## Versioning of definition files

Each definition file carries a version number formatted as `major.minor.patch`, where only major version changes may cause incompatabilities with derived classes. Minor version changes may be due to newly added classes or newly added properties. Patches are bug-fixes or different descriptions of existing properties. The version number is indicated in definition files by defining a class `providerId.version` which extends the `bas.version` class, and lists version numbers for each of the provider definition files that it depends on. 

## Translation of BAS-Schema to JSON-Schema

The translation to regular JSON-Schema serves the purpose of being able to use regular JSON-Schema validation tools for BAS-Schema definition files.
For the translation, the following rules apply.
- the YAML is converted to JSON and parsed.
- the resulting tree is placed in a `definitions` field with the appropriate JSON-Schema context.
- `extends` is replaced by inserting all properties of the parent class in an 'addAll' construct, whereby a `{ $ref: link }` construct is used to link between documents.
- `propertyValues` are replaced by `const` properties.
- The `modifiers` list is kept for reference but has no meaning in JSON-Schema. For properties that reference an `instanceOf` class that has a `modifiers` list, the JSON-Schema type is specified as either 'string' (in case a shorthand string is used) or the type of the class, using the `anyOf` construct.
