---
layout: default
title: "Brain Addressing System"
---

# The Brain Addressing System (BAS)

A *brain address* is a pointer to a well-defined *location* in a well-defined *brain atlas*.
The *location* part can be as simple as an x,y,z-coordinate or brain region acronym.
The *brain atlas* part can be as simple as the combination of two acronyms:
that of the *brain atlas provider*, and that of the atlas that the provider hosts.
For example, the address
`brainaddress:sba/ABA_v3?coord=1,2,3`
points to coordinate (1,2,3) in atlas *ABA_v3* hosted by provider *sba*.

Skip the long read, paste a brain address here to resolve it: <div><input type="text" size="100" onclick="lookup(this)"/><div>

The BAS solves a number of common issues with brain coordinates:
- **issue**: Brain atlases are often published in the form of a data set, like an (average) MRI image or a stack of histological sections. To properly define a real-world coordinate in this data set, one needs to know: how the data is oriented in real-world space, what the point (0,0,0) refers to, and what the units are.  
**solution**: The atlas providers host for each atlas a *definition file* that contains default settings for orientation, origin and units.
- **issue**: Existing data that refers to a brain atlas may not use the same orientation, origin and units as the defaults specified by the atlas provider.  
**solution**: In the location part of the address, the alternative orientation, origin and units can be specified. In fact, it is recommended that these are always specified to make the address more self-contained.
- **issue**: When brain atlases are published, they often do not come with an acronym. Different user groups of the atlas use their own name to refer to it.  
**solution**: User groups (example: 'The Human Brain Project') can register as a provider and maintain a list of atlases that they support.
- **issue**: Volumetric data (such as MRI) files typically contain transformations from voxel space to real-world space, but there is often no meta-data field to describe what atlas this real-world space is aligned with.  
**solution**: Depending on the situation, the applicable *brain atlas* can be specified as
  1. An additional meta-data field, either inside or separate from the file.
  - Part of the filename, using the *tokenized* form of the brain address.

The BAS solves the above issues, but may raise some questions:
- **Q**: A brain address contains provider and atlas acronyms, how to interpret these?  
**A**: At the minimum, this website (brainaddress.org) maintains an automatically updated list of providers and (versioned) atlas definition files that they host, at <a href="providers">`https://brainaddress.org/providers`</a>. In addition, the URL-version of a brain address (replace `brainaddress:` by `https://brainaddress.org/`) points to a page with information about the address, its atlas and available tools/viewers.
- **Q**: Multiple providers may use the same brain atlas but use different acronyms and definition files for it.  
**A**: Besides hosting *atlases*, atlas providers can also host *transformations*. They can define an identity transform to link their atlas to the same atlas hosted by other parties and vice versa.
- **Q**: A data set is aligned to an atlas that is not hosted by any provider. What to do?  
**A**: Either become a provider and host the atlas, or use the special provider acronym `my`. You can link a `brainaddress:my/atlas` address to a known atlas later on by defining a *transformation*.

## Address format
The compact form of a brain address is an URI that starts with the protocol 'brainaddress:', followed by the provider/atlas path (such as sba/ABA_v3), followed by a question mark, followed by a set of key-value pairs that describe the location (such as ?coord=1,2,3&unit=mm&orientation=RAS&origin=bregma).
Together this becomes: `brainaddress:sba/ABA_v3?coord=1,2,3&unit=mm&orientation=RAS&origin=bregma`.  
The address can also be <a href="shorthand.md">written as a shorthand</a> that is designed to be used as part of a filename.

## Become a provider
The system is open to organizations or individuals who want to provide brain atlas definitions or transformations.
To become a provider, register at https://brainaddress.org/providers/signup.md with
- a provider acronym
- a provider home page
- a provider content page at which you will host brain atlas definitions and transformations.
  If you <a href="docs/atlasdefinition.md">follow the instructions</a>, these files are auto-discovered and cached by brainaddress.org.

<script type="text/javascript">
function lookup(elem) {
  console.log(elem);
}
</script>
