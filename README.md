# @static-pages-js

> This project is currently in development by a single developer. There is much left to do. I plan to stick to this project and release updates and new components as my free time allows.

Yet another static pages generator for small and mid-sized projects written in JavaScript (TypeScript).

## Features

It will support a lot of data sources for input
- YAML
- Markdown (front-matter style)
- JSON
- Toml
- Ini
- Databases

And a lot of renderers for the output
- Twig
- ejs
- Mustache
- Pug
- Nunjucks
- Dot
- Jade
- Handlebars
- Underscore
- Placeholder

## Usage / Examples

You can use JS API to generate pages; see an example repository: [https://github.com/staticpagesjs/example-site-js](https://github.com/staticpagesjs/example-site-js)

Alternatively there is a CLI tool to help you; see an example repository: [https://github.com/staticpagesjs/example-site-cli](https://github.com/staticpagesjs/example-site-cli)

## Roadmap

| % | Task | Details |
|---|------|---------|
| stable | [Core module](https://www.npmjs.com/package/@static-pages/core) | The main component. Wires the readers writers and controllers together. |
| stable | [CLI module](https://www.npmjs.com/package/@static-pages/cli) | Allows to easily use the static generator from the command line. |
| stable | [Docker image](https://hub.docker.com/repository/docker/staticpages/cli) | A docker image that contains my packages ready to use in a build process. |
| 2% | [Documentation/project page](https://staticpagesjs.github.io/) | This site you are looking now. |
| stable | [file-reader](https://www.npmjs.com/package/@static-pages/file-reader) | Generic reader implementation. |
| stable | [file-writer](https://www.npmjs.com/package/@static-pages/file-writer) | Generic writer implementation. |
| stable | [markdown-reader](https://www.npmjs.com/package/@static-pages/markdown-reader) | Markdown reader implementation. |
| not started | toml-reader | Toml reader implementation. |
| not started | ini-reader | Ini reader implementation. |
| stable | [yaml-reader](https://www.npmjs.com/package/@static-pages/yaml-reader) | Yaml / json reader implementation. |
| stable | [twig-writer](https://www.npmjs.com/package/@static-pages/twig-writer) | Twig template writer, uses [Twing](https://www.npmjs.com/package/twing). |
| stable | [nunjucks-writer](https://www.npmjs.com/package/@static-pages/twig-writer) | [Nunjucks](https://www.npmjs.com/package/nunjucks) template writer. |
| beta | [ejs-writer](https://www.npmjs.com/package/@static-pages/ejs-writer) | [EJS](https://www.npmjs.com/package/ejs) template writer. |
| beta | [mustache-writer](https://www.npmjs.com/package/@static-pages/mustache-writer) | [Mustache](https://www.npmjs.com/package/mustache) template writer. |
| not started | pug-writer | Will be implemented after the mustache-writer |
| not started | dot-writer | Will be implemented after the pug-writer |
| not started | handlebars-writer | Will be implemented after the dot-writer |
| not started | jade-writer | Will be implemented after the handlebars-writer |
| not started | underscore-writer | Will be implemented after the jade-writer |
| not started | placeholder-writer | Will be implemented after the underscore-writer. Its a simple .replace() implementation without any third party template engine. |

Readers for various databases (oracle, mysql, postgre, mongo etc.) will considered and implemented after the roadmap above are complete.

## Your own readers and writers

Input readers and output writers are easily extensible allowing you to make your own implementation.

### Example reader #1

This version implements everything from scratch.

```js
const fs = require('fs');
const path = require('path');
const glob = require('fast-glob');

module.exports = ({ cwd = 'pages', pattern = '**/*.json' } = {}) => ({
  [Symbol.iterator]() {
    const absCwd = path.resolve(cwd);
    const files = glob.sync(pattern, { cwd: absCwd, absolute: true });
    return {
      next() {
        const file = files.shift();
        if (!file) { // no more input
          return { done: true };
        }

        const relativePath = path.relative(absCwd, file);
        const extName = path.extname(file);
        const payload = JSON.parse(fs.readFileSync(file, 'utf-8'));

        const data = {
          // implement header field as you feel, add more if needed
          header: {
            cwd: absCwd,
            path: relativePath,
            dirname: path.dirname(relativePath),
            basename: path.basename(relativePath, extName),
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

This version uses the `@static-pages/file-reader` as a base implementation. Enables incremental builds out-of-the-box for your reader.

```js
const fileReader = require('@static-pages/file-reader').default;

module.exports = ({ cwd = 'pages', pattern = '**/*.json', incremental = false } = {}) => ({
  *[Symbol.iterator]() {
    for (const raw of fileReader({ cwd, pattern, incremental })) {
      const payload = JSON.parse(raw.body);
      yield {
        header: raw.header,
        ...payload,
      };
    }
  }
});
```

### Example writer #1

The `JSON.stringify` should be replaced with your own template rendering logic.

```js
const fs = require('fs');
module.exports = ({ outDir = 'build' }) => (
  (data) => {
    fs.writeFileSync(
      outDir + '/' + (data?.header?.path || 'unnamed'),
      JSON.stringify(data, null, 4)
    );
  }
);
```

### Example writer #2

This version uses the `@static-pages/file-writer` as a base implementation. The `JSON.stringify` should be replaced with your own template rendering logic.

```js
const fs = require('fs');
const fileWriter = require('@static-pages/file-writer').default;
module.exports = ({ gloabls = {}, ...rest }) => {
  const writer = fileWriter({
    ...rest,
    renderer: JSON.stringify(data, null, 4)
  });
  return d => writer(d);
};
```
