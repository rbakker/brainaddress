---
layout: default
title: "Provider Instructions"
---
# Provider instructions

The first step to become a provider is to get access to a publicly accessible storage space on the web where you can create directories and host files. For personal projects we recommend to use Github, as a no-cost solution that has the benefit of version control. 
If you create a repository 'BAS-files' there (see <a href="https://guides.github.com/activities/hello-world/">github tutorial</a>), then the files in your master branch are publicly accessible at:
*https&#58;//github.com/&lt;username>/BAS-files/tree/master/*, where username is your github username. Example: <a href="https://github.com/rbakker/BAS-files/tree/master/">https://github.com/rbakker/BAS-files/tree/master/</a>.

The second step is to sign up as a provider. You need to supply
- A short provider name (max. 16 characters, aim for 10 at most). For personal projects, this can be your github user name. It can also be an institute or project abbreviation. 
- A long provider name (max. 96 characters, aim for 60). For personal projects, this can be your name and affiliation. It can also be an institute or project name. 
- A home page url. This can be a link to your personal home page, or to an institute or project home page. If using a link to a Github repository, make sure it has a README.md file that describes the project.
- A BAS url. This is the place where you host definition folders and files. This can be the link to the master branch in a Github repository, see above.
- A contact name and email address. This name and address are used by our web-crawler to send you notifications. They will not be made public.

When you are ready, proceed to the <a href="/provider-signup.html">signup page</a>.

### Creating definition files

There are four types of definition files that you can provide:
1. Atlases
2. Transforms
3. Transformations
4. ROI-generators

To create a file in one of these categories, first create an all-lowercase folder with the category name (one of: atlases, transforms, transformations, roi-shapes), and next create a definition file in this folder with the name '<acronym>.json', where acronym is the short name of the object that you define. For the contents of this file, see the appropriate page:
- <a href="brainatlas.html">Atlas definitions</a>
- Transform definitions (in progress)
- Transformation definitions (in progress)
- ROI-generator definitions (in progress)
