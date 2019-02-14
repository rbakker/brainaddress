# brainaddress
A brain address points to a location in a reference (brain-)space, publicly hosted by one of our registered brain atlas providers.

The address can take three forms:
- as an url-styled address: https://provider.brainaddress.org/reference-space?coord=location&other_options
- as an email-styled address coord=location&other_options@reference-space.provider.brainaddress.org
- as a short token that can be part of a file name and denotes the reference space that the file contents are registered to.

The purpose of the brain addressing system (BAS) is to have a universal way to refer to a location in the brain. 
The system is open to organizations or individuals who want to provide one or more reference spaces.
To use BAS as a provider, you need to register at brainaddress.org/providers
with 
- a provider-acronym
- a provider-url, which must host one or more reference-spaces in the form of small meta-data files which are
  auto-discovered and cached by brainaddress.org.

The meta-data files may point to brain atlases that are hosted elsewhere on the web by other parties, the purpose of the BAS-provider is to define a set of default and alternative choices (orientation, resolution, origin, version) that apply to the atlas.  
If multiple providers create reference spaces for the same underlying brain atlas, then this can be resolved by defining *transformations*.

# transformations between reference spaces
Besides reference spaces, brain atlas providers can define transformations between brain spaces. The brain addressing system natively only supports affine transformations, but it has a mechanism to include custom nonlinear mappings. Transformation meta-data files are auto-discovered by brainaddress.org and used throughout the various services. 

A complete specification of the brain addressing system can be found at https://brainaddress.org.


