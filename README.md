# @static-pages-js

> This project is currently in development by a single developer. There is much left to do. I plan to stick to this project and release updates and new components as my free time allows.

Yet another static pages generator for small and mid-sized projects written in JavaScript (TypeScript).

## Concepts

I try to build this project as a toolbox, where each functionality is separated into its small well testable, documentable package, and build a documentation page later that links these components together with examples and usage cases.

The main component is the `@static-pages/core` which is the glue between the `reader`, the `controller` and the `writer` modules.

How does this work?
- __Readers__ provides an iterable list of page data.
- __Controllers__ can manipulate and extend each data object.
- __Writers__ render the final output for you.

## Features (and the future)

### File input and output
There are numerous sources to consider when it comes to **reading** and **writing** data. While the filesystem may be the most common source, other options such as web APIs to access Github, GitLab or other Git repositories can also be utilized.

I would like to provide the following packages for these file IO related tasks:
- `@static-pages/fileio`: reader and writer which uses the filesystem.
- `@static-pages/gitio`: a special reader and writer which can sit on a git repository on the local filesystem, and read/write specific branches without checkouts. My use case here is producing a live website in a repository using data from a different source repository, while enabling everyone to work on their separate branches.
- `@static-pages/gitlabio`: a reader and writer to allow you to access GitLab repository files.
- `@static-pages/githubio`: a reader and writer to allow you to access Github repository files.

Under consideration: SQL database wrapper packages for SQLite, Oralce, Postgre, Mongo, and any other popular ones.

> The SQL sources surely not my primary targets. It will be done when everything is ready and when I can't improve other parts.

### Formats
**Parsers** can understand and build an object from the raw data that the readers are providing. For instance, if we consider JSON data saved in plain text files, they can be likened to `JSON.parse`.
There are many libraries out there, so I won't try to implement any of these. The listed readers above will accept a `parser` property where you could plug in your own parser.

I plan to provide examples later with JSON, YAML, Markdown, Toml, INI inputs with third party libraries.

**Renderers** can transform your data to a webpage with a template. Again there are a lot of packages out there doing this better than I ever could. However I will try to make and maintain a few wrappers for Twig, EJS, Mustache and Pug for convience and to provide some examples.

### Convience packages
For the basic use cases I'm planning a few packages to merge together some functionality.

Lets say you have TWIG templates and rendering to filesystem. Here comes the `@static-pages/twig-writer` package to the rescue. Saves you the `@static-pages/fileio` and `@static-pages/twig-renderer` (or a third party package) install and configuration hassles.

My plans to release and maintain these packages:
- `@static-pages/yaml-reader`: read YAML and JSON
- `@static-pages/markdown-reader`: read front-matter style markdown
- `@static-pages/twig-writer`: renders twig templates to filesystem

And the following are under consideration (they exists now but I'm not sure its needed):
- `@static-pages/ejs-writer`: renders ejs templates to filesystem
- `@static-pages/mustache-writer`: renders mustache templates to filesystem
- `@static-pages/pug-writer`: renders pug templates to filesystem
- `@static-pages/nunjucks-writer`: renders twig-like templates to filesystem

### Server side and administration
Lately my primary focus is on making administration interfaces and linking our existing workflow together with our static pages powered sites.
Currently we are experimenting with ideas like:
- JSON schema based, automatically generated admin forms.
- Workflows with multiple roles, role based access and editing.
- And keeping everything simple but extensible.

I would like to build a component which can run on the serverside, and can provide an admin page for editors. Validating by editor roles and data schemas, building on save, with live previews and many more.
A lot can change until I'm ready with a working prototype, but one thing I'd definitely like to provide in the Static Pages project is a decoupled admin user interface alongside the existing generator.


## Usage / Examples

Here you can find a few example repositories that are built to demonstrate the idea and to provide a template for you.

- You can use JS API to generate pages: [https://github.com/staticpagesjs/example-site-js](https://github.com/staticpagesjs/example-site-js)

- Alternatively there is a CLI tool to help you: [https://github.com/staticpagesjs/example-site-cli](https://github.com/staticpagesjs/example-site-cli)


## Current status

| % | Task | Details |
|---|------|---------|
| stable | [Core module](https://www.npmjs.com/package/@static-pages/core) | The main component. Wires the readers writers and controllers together. |
| needs update | [CLI module](https://www.npmjs.com/package/@static-pages/cli) | Allows to easily use the static generator from the command line. |
| needs update | [Docker image](https://hub.docker.com/repository/docker/staticpages/cli) | A docker image that contains my packages ready to use in a build process. |
| 4% | [Documentation/project page](https://staticpagesjs.github.io/) | This site you are looking now. |
| deprecated | [file-reader](https://www.npmjs.com/package/@static-pages/file-reader) | Generic reader implementation. Will be merged to `fileio`. |
| deprecated | [file-writer](https://www.npmjs.com/package/@static-pages/file-writer) | Generic writer implementation. Will be merged to `fileio`. |
| stable | [markdown-reader](https://www.npmjs.com/package/@static-pages/markdown-reader) | Markdown reader implementation. |
| stable | [yaml-reader](https://www.npmjs.com/package/@static-pages/yaml-reader) | Yaml / json reader implementation. |
| stable | [twig-writer](https://www.npmjs.com/package/@static-pages/twig-writer) | Twig template writer, uses [Twing](https://www.npmjs.com/package/twing). |
| stable | [nunjucks-writer](https://www.npmjs.com/package/@static-pages/twig-writer) | [Nunjucks](https://www.npmjs.com/package/nunjucks) template writer. |
| stable | [ejs-writer](https://www.npmjs.com/package/@static-pages/ejs-writer) | [EJS](https://www.npmjs.com/package/ejs) template writer. |
| stable | [mustache-writer](https://www.npmjs.com/package/@static-pages/mustache-writer) | [Mustache](https://www.npmjs.com/package/mustache) template writer. |
| stable | [pug-writer](https://www.npmjs.com/package/@static-pages/pug-writer) | [Pug](https://www.npmjs.com/package/pug) template writer. |


## Cancelled packages

These packages was on the roadmap previously, but got dropped because they are easily configurable with `fileio` and a third party package.

- toml-reader
- ini-reader
- dot-writer
- handlebars-writer
- jade-writer
- underscore-writer
- placeholder-writer


## Roadmap

1. Implement, test and document `fileio` package.
2. Implement, test and document `gitio` package.
3. Implement, test and document `gitlabio` package.
4. Review and deprecate current packages if needed.
5. Create a server-side REST API component.
6. Create an admin UI that uses the REST API or the Gitlab API directly.
7. Add documentation and examples where needed. Cookbook.
8. Determine whats next.


## Your own readers and writers

Input readers and output writers are easily extensible allowing you to make your own implementation.

### Example reader #1

This sample implements a simple JSON reader from scratch.

```js
import * as fs from 'fs';
import * as path from 'path';
import glob from 'fast-glob';

export default ({ cwd = 'pages', pattern = '**/*.json' } = {}) => ({
  [Symbol.iterator]() {
    const files = glob.sync(pattern, { cwd });
    const resolvedCwd = path.resolve(cwd);
    return {
      next() {
        const file = files.shift();
        if (!file) { // no more input
          return { done: true };
        }

        const extName = path.extname(file);
        const payload = JSON.parse(fs.readFileSync(file, 'utf-8'));

        const data = {
          // implement header field as you feel, add more if needed
          header: {
            cwd: resolvedCwd,
            path: file,
            dirname: path.dirname(file),
            basename: path.basename(file, extName),
            extname: extName,
          },
          ...payload,
        };

        return { value: data };
      }
    };
  }
});
```

### Example reader #2

This sample uses the `@static-pages/file-reader` as a base implementation. Enables incremental builds out-of-the-box for your reader.

> Tip: When you use the `staticpages/cli` Docker image, the `@static-pages/file-reader` is already pre-installed.

```js
import fileReader from '@static-pages/file-reader';

export default ({ cwd = 'pages', pattern = '**/*.json', ...rest } = {}) => ({
  *[Symbol.iterator]() {
    for (const raw of fileReader({ cwd, pattern, ...rest })) {
      yield {
        header: raw.header,
        ...JSON.parse(raw.body),
      };
    }
  }
});
```

### Example writer #1

The `JSON.stringify` should be replaced with your own template rendering logic.

```js
import * as fs from 'fs';

export default ({ outDir = 'dist' }) => (
  ({ value, done }) => {
    if (!done) fs.writeFileSync(
      outDir + '/' + (value?.header?.path || 'unnamed'),
      JSON.stringify(value, null, 4)
    );
  }
);
```

### Example writer #2

This sample uses the `@static-pages/file-writer` as a base implementation. The `JSON.stringify` should be replaced with your own template rendering logic.

> Tip: When you use the `staticpages/cli` Docker image, the `@static-pages/file-writer` is already pre-installed.

```js
import * as fs from 'fs';
import fileWriter from '@static-pages/file-writer';

export default (opts) => {
  const writer = fileWriter({
    ...opts,
    renderer: data => JSON.stringify(data, null, 4)
  });
  return d => writer(d);
};
```
