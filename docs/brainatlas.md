---
layout: default
title: "Brain atlas definition"
---

# Brain atlas definition

In BAS, a **brain atlas** is defined as a collection of spatially distributed data modalities and/or derived annotations, all registered to the same *template brain*. The template brain can be a single subject brain, or consist of (an average of) many brains that are spatially aligned to each other. An atlas can be as simple as a single MRI volume that serves as both the data modality and the template brain. BAS further requires that the template brain is placed in a 3-dimensional Euclidian space that meets three criteria:
1. The space must use real-world physical coordinates. This means that for image-based data modalities, a linear transformation that translates pixel/voxel indices to physical positions must be provided. Example: for an MRI volume, it is sufficient to specify one voxel as the origin, plus the size of the individual voxels in millimeters.
2. The coordinate axes, after transformation to real-world units, must each be assigned an <a href="orientationcodes.html">anatomical orientation</a>. Example: the (x,y,z)-axes have (right, anterior, superior) orientation.
3. The origin must be assigned to a clearly defined point in one of the data modalities. Example: the origin is the corner of voxel (0,0,0) in the T1-modality.

## Creating definition files

To *define an atlas*, a brain atlas provider must create two files in the folder 'atlases'.
1. An *atlas definition file* for each supported brain atlas. The file must be named `<basUrl>/atlases/<atlas>.json`, where `<basUrl>` is the bas-url specified when registering as a provider, and `<atlas>` is the id/acronym of the atlas.
2. An *index file*, named `<basUrl>/atlases/index.json`.

The **index file** is a JSON-formatted file that contains a list of acronyms of all defined brain-atlases. Only atlases in this list will be recognized as valid atlases. If the index file is missing, then the BAS-crawler will not be able to find any atlases, *unless* the webserver has a directory listing service (see for example <a href="https://wiki.apache.org/httpd/DirectoryListings">.htaccess Options +Indexes</a>). Example: you create two atlas definition files 'atlases/myatlas1.json' and 'atlases/myatlas2.json'. The file 'atlases/index.json' must contain the line `["atlas1","atlas2"]`.

The **atlas definition file** is a JSON-formatted file that contains a dictionary with keys (star indicates 'required'):
- `id`\*: [string(16)] The id/acronym of the brain atlas. It must be short (aim for 10 characters max., the limit is 16 characters) and consist of alpha-numeric characters including minuses and underscores. The `id` may end with a *major* version number, preceded by '_v'. Example: `ABA_v3`. See also the `version` field.
- `name`\*: [string(96)] The longer name (aim for 60 characters) that identifies the atlas. It should rather not mention the animal species. Example: 'DTI template of FirstAuthor et al. (2019)'.
- `species`\*: [suggested choices] The short English name of the animal species that the atlas applies to. Suggested choices are ['cat','ferret','mouse','rat','macaque','marmoset','human'], more species are allowed.
- `subSpecies`: [string] The English name of the animal sub-species that the atlas applies to. Examples: sprague dawley, cynomolgus.
- `strain`: [string] The (genetic) strain of the animal that the atlas applies to. Example: C57Bl/6J.
- `boundingBox`\*: [list] The bounding box is a crucial part of the definition. It contains minimum-maximum coordinates for each axis, such that (1) the box encloses the entire template brain, (2) the point (0,0,0) is located at a well-defined point, preferrably coinciding with the origin promoted by the atlas authors. The coordinates must be specified in **millimeters**. They are specified by three subfields:
  - `lpiCorner`\*: [number,number,number] The coordinates in mm of the [left,posterior,inferior]-corner (in that order) of the bounding box.
  - `rasCorner`\*: [number,number,number] The coordinates in mm of the [right-anterior-superior]-corner (in that order) of the bounding box.
  - `motivation`: [string] A textual motivation for the choice of bounding box (and implicit  origin). Example: "Real-world extent of the T1 image, with the corner of the 0-th voxel as the origin."
- `hemisphere`: [predefined choices] The hemisphere that the atlas covers. One of ['left','right,'both'].
- `landmarks`: [list] Dictionary with landmarks that can be used as alternative origins for the atlas. It is recommended to specify at least a landmark for coordinage [0,0,0], the native origin. The dictionary keys serve as the landmark acronyms (aim for 10 characters max.). The values contain the fields:
  - `coord`: [number,number,number] The coordinate of the landmark, in the space described by the previously defined boundingBox (i.e., with right, anterior, superior ordering).
  - `name`\*: [string(96)] Longer name of the landmark (aim for 60 characters).
  - `description`: [string] The description field can be used to provide instructions on how to identify the landmark.
- `url`\*: [url] The home page of the atlas, or the project that generated the atlas.
- `definingCitations`\*: [list] List of citations that describe the atlas. Each citation is a dictionary with the following fields:
  - `authors`: list of LastName Initials strings, like "Bakker R"
  - `title`: title of the publication
  - `year`: year of the publication
  - `journal`: <a href="https://images.webofknowledge.com/images/help/WOS">official journal abbreviation</a>
  - `doi`\*: document object identifier, or web page if no DOI is available
- `version`\*: [major.minor.patch] the version of the atlas, in <a href="https://semver.org/">major.minor.patch notation</a>. The major version changes whenever data registered to previous atlas versions is *no longer optimally aligned* with the current version. This happens when the *template brain* (see above) gets modified, or any of the bounding box coordinates. A major version change requires a new acronym for the atlas. We suggest to use the postfix `_v<major>`, where major is the major version number. Example: ABA_v3. The minor version changes on every significant update of the atlas that does not affect the template brain. A patch should be fully backwards compatible.
- `release`: [predefined choices] The stage of the <a href="https://en.wikipedia.org/wiki/Software_release_life_cycle">release cycle</a> of the atlas. One of ['pre-alpha','alpha','beta','stable','end-of-life']. Atlases with the 'pre-alpha' level will not be harvested by the BAS portal at all, while 'stable' atlases appear at the top of lists. The default is 'alpha'.

Note that this specification may change, but always in such a way that older definition files get interpreted correctly. It is not necessary to include a 'BAS version number' in the file.
