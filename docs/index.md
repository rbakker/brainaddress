---
layout: default
title: "Brain Addressing System"
---

# The Brain Addressing System (BAS)

A *brain address* is a pointer to a well-defined *location* in a well-defined *brain atlas*.
The *location* part can be as simple as an x,y,z-coordinate or brain region acronym.
The *brain atlas* part can be as simple as the combination of two acronyms:
that of the *brain atlas provider*, and that of an atlas that the provider defines.
For example, the address
`brainaddress:sba/ABA_v3?origin=bregma#1,2,3`
points to coordinate (1,2,3) in atlas *ABA_v3* hosted by provider *sba*, using *bregma* as the origin.

Skip the long read, paste a brain address here to resolve it: <input type="text" size="100" onchange="lookup(event)"/>

BAS solves a number of common issues with brain coordinates:
- **issue**: Many experimental data sets are published in a form already registered to a brain atlas. However, it is often not clear what 'incarnation' of the atlas is referred to. The version with micrometer units? The version with the origin at bregma? The version with the x- y- and z-axes pointing to the right, anterior, superior sides of the brain?  
**solution**: In BAS, an atlas is always referenced in conjunction with the chosen unit, orientation and origin. Atlas providers supply *atlas definition files* in which valid choices are defined, in addition to default choices defined by BAS.
- **issue**: When brain atlases are published, they often do not come with an acronym. Different user groups of the atlas use their own name to refer to it.  
**solution**: Users or user groups (example: 'The Human Brain Project') can register as a provider and maintain a list of atlases that they support.  
- **issue**: Volumetric data (such as MRI) files typically contain transformations from voxel space to real-world space, but there is often no meta-data field to describe whether that space is actually a brain atlas.  
**solution**: Depending on the situation, the applicable *brain atlas* can be specified as
  1. An additional 'brain-address' meta-data field, either inside or separate from the file.
  2. Part of the filename, using the *tokenized* form of the brain address.

BAS is designed to be simple. We hope the list of frequently asked questions will remain short:
- **Q**: A brain address contains provider and atlas acronyms, how to interpret these?  
**A**: This website (brainaddress.org) maintains an automatically updated list of providers and (versioned) atlas definition files that they host, at <a href="providers.html">https://brainaddress.org/providers.html</a>. In addition, the URL-version of a brain address (replace `brainaddress:` by `https://brainaddress.org/`) points to a page with information about the address, its atlas and available tools/viewers.
- **Q**: Multiple providers may use the same brain atlas but use different acronyms and definition files for it.  
**A**: Besides hosting *atlases*, atlas providers can also host *transforms*, *transformations* (which apply *transforms* to map data between atlases) and *ROI-generators* (which define regions of interest). They can use the identity transform to link their atlas to the same atlas hosted by other parties and vice versa.
- **Q**: A data set is aligned to an atlas that is not defined by any provider. What to do?  
**A**: Either become a provider and define the atlas (see below). 
If that is too much work, use the special provider acronym `my` that indicates a private atlas. You can link a `brainaddress:my.<atlas>` address to a known atlas later, by defining a transformation.
- **Q**: I am a provider; how do I tell BAS that I updated an atlas definition?  
**A**: BAS automatically scans all providers several times per day.

## Address format
The regular form of a brain address is a URI that starts with the protocol 'brainaddress:', followed by the path &lt;provider>/&lt;atlas>, followed by a query that contains atlas parameters, and finally with a hash that specifies a location or region of interest. Example:  
`brainaddress:sba/ABA_v3?unit=mm&orientation=RAS&origin=bregma#1,2,3`.
The address can also be written as a shorthand that is designed to be used as part of a filename. For details, visit the <a href="addressformat.html">address format specification</a> page.

## Become a provider
A BAS provider does not need to 'own' or host brain atlases. The provider only hosts *definition files* that describe atlases, transforms or transformations. For the hosting, the provider can use a website or a github repository.
The system is open to organizations or individuals who want to provide brain atlas definitions or transformations.
To become a provider, follow the <a href="provider-instructions.html">detailed provider instructions</a>. You will need:
- a provider acronym
- a provider home page or github repository
- a provider content page (github folder) at which you will host definition files.  

All definition files are auto-discovered and cached by brainaddress.org.

<script type="text/javascript">
function lookup(evt) {
  const addr = evt.target.value;
  const matches = addr.match(/(brainaddress:|https?:\/\/brainaddress.org\/)([^\/]+\/[^?]+)\?([^=]+=[^&]+)*/);
  if (matches) {
    console.log(JSON.stringify(matches[2]+'?'+matches[3]));
    window.open(matches[2]+'?'+matches[3]);
  } else {
    alert('Invalid address.');
  }
  console.log(event,addr,matches);
}
</script>
