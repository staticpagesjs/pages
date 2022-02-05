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
| not published yet | [Docker image](https://hub.docker.com/repository/docker/lionel87/static-pages-js) | A docker image that contains my packages ready to use in a build process. |
| 1% | [Documentation/project page](https://staticpagesjs.github.io/) | This site you are looking now. |
| 50% beta | [markdown-reader](https://www.npmjs.com/package/@static-pages/markdown-reader) | Generic reader implementation. incremental builds and docs left to be done |
| 50% beta | [json/yaml-reader](https://www.npmjs.com/package/@static-pages/yaml-reader) | Generic reader implementation. incremental builds and docs left to be done |
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

### Example reader (json)
```js
const fs = require('fs');
const path = require('path');
const glob = require('fast-glob');

module.exports = ({ cwd, pattern = '**/*.json' } = {}) => ({
  [Symbol.iterator]() {
    const absCwd = path.resolve(process.cwd(), cwd);
    const files = glob.sync(pattern, { cwd: absCwd, absolute: true });
    return {
      next() {
        const file = files.pop();
        if (!file) { // no more input
          return { done: true };
        }

        const relativePath = path.relative(absCwd, file);
        const extName = path.extname(file);
        const payload = JSON.parse(fs.readFileSync(file, 'utf-8'));
        
        const data = {
          // implement header field as you feel
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

### Example writer

This could be much comlex than this below. The example just json stringify but the same way it could render with a third party library.

```js
const fs = require('fs');
module.exports = ({ key = 'url' } = {}) => (data) => fs.writeFileSync(data[key] || 'unnamed', JSON.stringify(data, null, 4));
```
