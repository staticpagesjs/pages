# Static Pages JS

> This project is in development by a single developer. There is much left to do, and this project page is also a bit outdated now. I plan to stick to this project and release updates and new components as my free time allows.

Yet another static pages generator for small and mid-sized projects written in JavaScript (TypeScript).

## Concepts

I try to build this project as a toolbox, where each functionality is separated into its small well testable, documentable package, and build a documentation page later that links these components together with examples and use cases.

The main component is the [`@static-pages/core`](https://npmjs.com/package/@static-pages/core) which provides the base functionalities. Other packages adds functionalities on top of that. A starter package that links together a few useful things is available at [`@static-pages/starter`](https://npmjs.com/package/@static-pages/starter). It is a good point to start.

### How does the static page generation works?

The user has to define `Route`s that describes a workflow for a batch of "similar" pages.
A route has a source and a destination, and optionally a controller that manipulates the data in between.

A source is not more than an iterable list of page data. There are helpers to read this data from
various sources (local fs, Gitlab, Github, etc.) and file formats (JSON, YAML, Markdown, etc.).

A destination is simply a callback function that renders and stores the actual page. There are helpers to render with popular template engines and to save them in the desired storage type.


## Features (and the future)

### File input and output
There are numerous sources to consider when it comes to **reading** and **writing** data. While the filesystem may be the most common source, other options such as web APIs to access Github, GitLab or other Git repositories can also be utilized.

I would like to provide the following packages for these file IO related tasks:
- `@static-pages/io`: helpers to read and write to an abstract filesystem.
- `@static-pages/gitfs`: a special filesystem implementation which can look into a local git repository, and read/write specific branches without checkouts. My use case here is to produce a live website served from a git repository where each branch corresponds to a source git repository branch.
- `@static-pages/gitlabfs`: a filesystem implementation to allow you to access GitLab repository files.
- `@static-pages/githubfs`: a filesystem implementation to allow you to access Github repository files.

Under consideration: SQL database wrapper packages for SQLite, Oralce, Postgre, Mongo, and any other popular ones.

> The SQL sources surely not my primary targets. It will be done when everything is ready and when I can't improve other parts.

### Formats
**Parsers** can understand and build an object from the raw data that the readers are providing. For instance, if we consider JSON data saved in plain text files, they can be likened to `JSON.parse`.
There are many libraries out there, so I won't try to implement any of these. The listed readers above will accept a `parser` property where you could plug in your own parser.

I plan to provide examples later with JSON, YAML, Markdown, Toml, INI inputs with third party libraries.

**Renderers** can transform your data to a webpage with a template. Again there are a lot of packages out there doing this better than I ever could. However I will try to make and maintain a few wrappers for Twig, EJS, Mustache and Pug for convience and to provide some examples.

Some popular template engines will get a bridge package under the `@static-pages` namespace.
These packages will help to integrate these template engies better in the system.

Planned:
- [`@static-pages/twig`](https://npmjs.com/package/@static-pages/twig)
- `@static-pages/ejs`
- `@static-pages/pug`
- `@static-pages/nunjuck`
- `@static-pages/mustache`

Not on the planned list, because its easy to configure without a bridge package:
- `dot`
- `handlebars`
- `jade`
- `underscore`
- many more

### Server side and administration
Lately my primary focus is on making administration interfaces and linking our existing workflow together with our static pages powered sites.
Currently we are experimenting with ideas like:
- JSON schema based, automatically generated admin forms.
- Workflows with multiple roles, role based access and editing.
- And keeping everything simple but extensible.

I would like to build a headless CMS that can provide an admin page, simlar what [DecapCMS](https://decapcms.org/) does, but in a more programmable and flexible way.
Validating by editor roles and data schemas, building on save, with live previews and many more.
A lot can change until I'm ready with a working prototype, but one thing I'd definitely like to provide in the Static Pages project is a decoupled admin user interface alongside the existing generator.

### Outdated packages
There are some outdated packages in the `@static-pages` namespace that will be marked as deprecated after some time.

These packages still can work together with the latest static pages engine, and probably always will, but they are built around an outdated concept or there are better alternatives available.

Previously the reading and writing was split into multiple different packages, but since `@static-pages/io` and the abstract filesystem these became obsolete:

- `@static-pages/file-reader`
- `@static-pages/file-writer`
- `@static-pages/yaml-reader`
- `@static-pages/markdown-reader`
- `@static-pages/twig-writer`
- `@static-pages/nunjucks-writer`
- `@static-pages/ejs-writer`
- `@static-pages/mustache-writer`
- `@static-pages/pug-writer`

The following package(s) became deprecated thanks to the redesign of the bridge packages for template engines:

- `@static-pages/twig-renderer` (use `@static-pages/twig`)


## Usage / Examples

Here you can find an example repository that are built to demonstrate the idea and to provide a template for you.

[https://github.com/staticpagesjs/example-site](https://github.com/staticpagesjs/example-site)


## Current status

| % | Component | Details |
|---|------|---------|
| stable | [starter](https://www.npmjs.com/package/@static-pages/starter) | A good package to test this tool with. |
| near-stable | [core](https://www.npmjs.com/package/@static-pages/core) | The main component. |
| stable | [io](https://www.npmjs.com/package/@static-pages/io) | Helpers to read and write page data. Needs unit tests. |
| 80% | [nodefs](https://www.npmjs.com/package/@static-pages/nodefs) | Adds "incremental build" support to nodejs fs module. |
| 50% | [gitfs](https://www.npmjs.com/package/@static-pages/gitfs) | Git repository fs. |
| TODO | [gitlabfs](https://www.npmjs.com/package/@static-pages/gitlabfs) | Gitlab repository fs. |
| TODO | [githubfs](https://www.npmjs.com/package/@static-pages/githubfs) | Github repository fs. |
| 1% | [Documentation/project page](https://staticpagesjs.github.io/) | This site you are looking now. |
| needs update | [CLI module](https://www.npmjs.com/package/@static-pages/cli) | Allows to easily use the static generator from the command line. |
| needs update | [Docker image](https://hub.docker.com/repository/docker/staticpages/cli) | A docker image that contains my packages ready to use in a build process. |


Visit on NPM: [static-pages](https://www.npmjs.com/search?q=%40static-pages)


## Roadmap

1. Create a node filesystem implementation that can process files incrementally (only read changed ones).
2. Create a local git filesystem implementation.
2. Create a Gitlab filesystem implementation.
3. Create a Github filesystem implementation.
4. Review and deprecate outdated packages if needed.
5. Create a server-side REST API component.
6. Create a CMS admin UI that uses the REST API or the Gitlab/Github API.
7. Add documentation and examples where needed. Cookbook.
8. Determine whats next.
