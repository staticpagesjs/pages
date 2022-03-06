# @static-pages-js

> This project is currently in development by a single developer. There is much left to do. I plan to stick to this project and release updates and new components as my free time allows.

Yet another static pages generator for small and mid-sized projects written in JavaScript (TypeScript).

## Features

It will support a lot of data sources for input
- YAML
- Markdown (front-matter style)
- JSON
- Databases

And a lot of renderers for the output
- Twig
- ejs
- Mustache
- Handlebars
- Pug
- Underscore
- Jade
- Dot

## Roadmap

| % | Task | Details |
|---|------|---------|
| stable | [Core module](https://www.npmjs.com/package/@static-pages/core) | The main component. Wires the readers writers and controllers together. |
| beta | [CLI module](https://www.npmjs.com/package/@static-pages/cli) | Allows to easily use the static generator from the command line. Needs care on security front. |
| beta | [Docker image](https://hub.docker.com/repository/docker/staticpages/cli) | A docker image that contains my packages ready to use in a build process. |
| 2% | [Documentation/project page](https://staticpagesjs.github.io/) | This site you are looking now. |
| stable | [markdown-reader](https://www.npmjs.com/package/@static-pages/markdown-reader) | Generic reader implementation. |
| stable | [markdown-reader](https://www.npmjs.com/package/@static-pages/markdown-reader) | Markdown reader implementation. |
| stable | [yaml-reader](https://www.npmjs.com/package/@static-pages/yaml-reader) | Yaml / json reader implementation. |
| 30% experimental | [twig-writer](https://www.npmjs.com/package/@static-pages/twig-writer) | Needs more care on security front and on API front. |
| not started | dot-writer | Will be implemented after the twig experimental |
| not started | ejs-writer | Will be implemented after the twig experimental |
| not started | handlebars-writer | Will be implemented after the twig experimental |
| not started | jade-writer | Will be implemented after the twig experimental |
| not started | mustache-writer | Will be implemented after the twig experimental |
| not started | nunjucks-writer | Will be implemented after the twig experimental |
| not started | pug-writer | Will be implemented after the twig experimental |
| not started | underscore-writer | Will be implemented after the twig experimental |

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
const reader = require('@static-pages/file-reader');

module.exports = ({ cwd = 'pages', pattern = '**/*.json', incremental = false } = {}) => ({
  *[Symbol.iterator]() {
    for (const raw of reader({ cwd, pattern, incremental })) {
      const payload = JSON.parse(raw.body);
      yield {
        header: raw.header,
        ...payload,
      };
    }
  }
});
```

### Example writer

The `JSON.stringify` should be replaced with your own template rendering logic.

```js
const fs = require('fs');
module.exports = ({ buildDir = 'build' }) => (
  (data) => {
    fs.writeFileSync(
      buildDir + '/' + (data?.header?.path || 'unnamed'),
      JSON.stringify(data, null, 4)
    );
  }
);
```
