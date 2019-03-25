---
layout: default
title: "Brain Addressing System - Address format"
---

# Address format

Before describing the formats, it is important to realize that a brain address always points to a location in a *brain atlas*. To assign an address to data from a new brain, the new brain must first be transformed to match the template brain of that atlas. This process is called *registration*. We call the resulting space **atlas-registered space**. A major issue that BAS solves, is that one and the same brain atlas is often used in many different incarnations. Light-microscopists use a version with micrometer coordinates, MRI-scientists prefer the atlas to match the orientation of their scanner, and electrophysiologists may want to transform the atlas origin to bregma. When each of these user groups claims that their data is registered to a given atlas, it is still a complex puzzle to merge their data to a single space. The BAS solution is not to re-register the existing data to a more standardized atlas space, but rather to have the data specify the choices it made for units, anatomical orientation, and origin. This is what is encoded in the brain address.

A BAS brain address can be formatted as a full address, in shorthand notation or as a JSON dictionary. The full notation looks like a web-address, and points to a page with detailed information about the address. The shorthand notation is designed to be used as part of a file name. The JSON format is the most verbose, ready to support extended features.

### Full notation
The full address uses an <a href="https://tools.ietf.org/html/rfc3986">URI notation</a>, which means that it consists of five components, formatted as `<scheme>:[//<authority>]<path>[?<query>][#<fragment>]`, where the square brackets indicate optional parts.
The five parts are used as follows:
- For *scheme>* and *authority*, there are two choices.  
Either the `brainaddress` scheme an no authority is used, so that the address becomes `brainaddress:<path>[?<query>][#<fragment>]`.
Or the `https` scheme and `brainaddress.org` authority is used, so that the address become a URL and looks like `https://brainaddress.org/<path>[?<query>][#<fragment>]`. Both choices represent the same address. The URL-variant can be used directly in a browser to retrieve a page with detailed information about the address.

- The *path* consists of two parts separated by a slash (/): the acronym of the provider, and the acronym of the brain atlas.
The provider acronym must be contained in the list of providers hosted at https://brainaddress.org/providers. The atlas acronym must be contained in the list of atlases of the selected provider, hosted at *https&colon;//brainaddress.org/&lt;provider>/atlases*. Note that these lists are automatically harvested from the provider's public BAS-pages. Example: with provider `sba` and atlas `ABA_v3`, the address becomes `brainaddress:sba/ABA_v3[?<query>][#<fragment>]`.
- The *query* consists of `key=value` pairs, separated by the ampersand sign (&amp;). The key-value pairs are used to supply optional parameters that indicate the *variant of the atlas space* that the supplied coordinates are registered to, in particular its orientation, unit and origin.  
Supported parameters are:
  - `unit`: the length-unit of the atlas-registered data. Possible values are m, mm, um, nm. The unit may be preceded by a number, as in `25.4mm`. The unit may also be preceded by multiple numbers, one for each dimension, separated by an x-sign, as in `0.01x1x0.01mm`. In this case, the unit represents the size of a voxel. 
  - `orientation`: the anatomical orientation of the atlas-registered space. In BAS, all atlas spaces are defined in <a href="orientationcodes">RAS+ orientation</a>. Any other orientation can be used by specifying an alternative 3-letter orientation code (optionally by a plus-sign).
  - `origin`: the *name* of the origin of the atlas-registered space. This name must be a valid origin name. In BAS, all atlas-spaces are defined in terms of a bounding box that encloses the template brain of the atlas. The atlas provider can choose this bounding box in such a way that the point (0,0,0) is at a *landmark*, such as bregma or anterior commissure, or at the first voxel of an MRI-image that comes with the atlas, or at the center of the bounding box, or anywhere else. Whatever the atlas provider choses becomes the *default* origin, and has the name 'zero'. In addition, the atlas provider may define the coordinates of additional *landmarks*, each of which can be used as an origin. BAS also defines two additional origin names: center (center of the bounding box), and ^corner (corner of the bounding box that has the smallest x-, y- and z-coordinates). The tilde in the `^corner` origin has a special meaning. Normally, when converting voxel coordinates to real-world coordinates, the real-world coordinate applies to the *center* of the voxel. But when the tilde is used, it means that the real-world coordinate of a voxel applies to the *corner* of the voxel (with the smallest x-, y- and z-coordinates).

- The *fragment* is used to specify the location in the brain atlas. Normally, it contains a triplet of x, y, z coordinates. Alternatively, the fragment may specify a *region of interest (ROI) shape*. These are functions, defined by atlas providers, that return the probability of being in a ROI for a given input coordinate. See the <a href="roi-shapes">ROI-shapes page</a>.

### Address token
The shorthand notation is designed to be used as part of a file name, to indicate the atlas space that the file contents are registered to. The notation consists of the same components, but formatted differently as in `bas{x,y,z@provider.atlas[.^]origin,unit,orientation}`. Here, `bas{}` indicates that a brain addressing system declaration follows. The `x,y,z@` part contains the coordinate. It can be omitted if only the *atlas space* is of interest. Between `atlas` and `origin` there must either be a dot (.) to indicate center-of-voxel alignment, or tilde (^) to indicate corner-of-voxel alignment. The `unit` and `orientation` parameters are optional and may appear in any order. Examples: `bas{1,2,3@sba.ABA_v3.ac,um,PIR}`, `bas{sba.ABA_v3^corner,LIP,mm}`.

### JSON notation
When brain addresses are used inside json-based file formats, the various components can also be written as a dictionary:  
```javascript
{
"provider": <provider>,  
"atlas": <atlas>,  
"coord": [<x>,<y>,<z>],  
"roi": {
  "cmd": <roi-generator>
  "params": >roi-generator parameters>
},
"unit": <unit>
"voxelsize": [<dx>,<dy>,<dz>],
"orientation": <orientation>,
"origin": <origin>,
}
```
where the parts between braces represent valid parameter values. 
Here, unit only accepts the unit name, the voxel size is in a separate parameter.
