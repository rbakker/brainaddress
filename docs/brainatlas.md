--
layout: default
title: "Brain Addressing System - Brain atlas"
---

# Brain atlas

In BAS, a **brain atlas** is defined as a collection of spatially distributed data modalities and/or derived annotations, all registered to the same *template brain*. The template brain can be a single subject brain, or consist of (an average of) many brains that are spatially aligned to each other. An atlas can be as simple as a single MRI volume that serves as both the data modality and the template brain. BAS further requires that the template brain is placed in a 3-dimensional Euclidian space that meets three criteria:
1. The space must use real-world physical coordinates. This means that for image-based data modalities, a linear transformation that translates pixel/voxel indices to physical positions must be provided. Example: for an MRI volume, it is sufficient to specify one voxel as the origin, plus the size of the individual voxels in millimeters.
2. The coordinate axes, after transformation to real-world units, must each be assigned an anatomical orientation. Example: the (x,y,z)-axes have <a href="orientationcodes.html">(right, anterior, superior)</a> orientation.
3. The origin must be assigned to a clearly defined point in one of the data modalities. Example: the origin is the corner of voxel (0,0,0) in the T1-modality.

## Defining an atlas

To *define an atlas*, a brain atlas provider must create two files.
1. An *atlas definition file* for each supported brain atlas. The file must be named `<basUrl>/atlases/<atlas>.json`, where `<basUrl>` is the bas-url specified when registering as a provider, and `<atlas>` is the id/acronym of the atlas.
2. An *index file*, named `<basUrl>/atlases.json`.

The **index file** is a JSON-formatted file that contains a list of acronyms of all hosted brain-atlases. Only atlases in this list will be recognized as valid atlases.

The **atlas definition file** is a JSON-formatted file that contains a dictionary with keys (star indicates 'required'):
- `id`\*: [string(16)] The id/acronym of the brain atlas. It must be short (aim for 10 characters max., the limit is 16 characters) and consist of alpha-numeric characters including minuses and underscores. The `id` may end with a *major* version number, preceded by '_v'. Example: `ABA_v3`. See also the `version` field.
- `name`\*: [string(96)] The longer name (aim for 60 characters) that identifies the atlas. It does not need to mention the animal species. Example: 'DTI template of FirstAuthor et al. (2019)'.
- `version`\*: [major.minor] the version of the atlas, in major.minor notation. The major version changes whenever data registered to previous atlas versions may no longer be *optimally aligned* with the current version. This happens when the *template brain* (see above) gets modified, or its embedding in physical space. The minor version changes on every update of the atlas that does not affect the template brain. The minor version extends beyond 9 by adding digits: after 0.9 it becomes 0.10.
- `species`\*: [suggested choices] The short English name of the animal species that the atlas applies to. Suggested choices are ['cat','ferret','mouse','rat','macaque','marmoset','human'], more species are allowed.
- `subSpecies`: [string] The English name of the animal sub-species that the atlas applies to. Examples: sprague dawley, cynomolgus.
- `strain`: [string] The (genetic) strain of the animal that the atlas applies to. Example: C57Bl/6J
- `downloadUrl`: [url] The web-address where data-modalities and annotations of the atlas can be found.
- `status`: [pre-defined choices] The readyness of the atlas. One of ['todo','started','finished','deprecated']. Atlases with the 'todo' status will not be harvested by the BAS portal, while 'finished' atlases appear at the top of lists. The default is 'started'.
- `boundingBox`\*: [list] The bounding box is a crucial part of the definition. It contains minimum-maximum coordinates for each axis, such that (1) the box encloses the entire template brain, (2) the point (0,0,0) is located at a well-defined point, preferrably coinciding with the origin promoted by the atlas authors. The coordinates must be specified in **millimeters** and ordered such that the x, y and z-axes have <a href="">+RAS</a> orientation.

Note that this specification may change, but always in such a way that older definition files get interpreted correctly. It is not necessary to include a 'specification version number' in the file.
