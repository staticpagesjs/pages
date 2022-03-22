# Setting up your first project

This project is unopinionated about almost everything; yet here are some recipes that are useful for starting your workflow. Default values for readers and writers are set to match these recipes.

## Example repositories

There are two example repositories that showcases what can be achieved with only a few lines of code.

- One uses the JS API to generate pages: [https://github.com/staticpagesjs/example-site-js](https://github.com/staticpagesjs/example-site-js)

- The other is using the CLI tool: [https://github.com/staticpagesjs/example-site-cli](https://github.com/staticpagesjs/example-site-cli)


## Directory strucutre

This structure is optimal for small sites, where only a few pagetypes exists.

```
project
|-- build - will contain the produced output
|-- build.config.yaml - configuration for the staticpages cli
|-- build.lib.js - contains every user defined JS logic
|-- globals.yaml - additional context for the template renderer (twig, nunjucks)
|-- messages - translation messages for the trans filter (twig, nunjucks)
|   |-- de.yaml
|   |-- en.yaml
|   `-- fr.yaml
|-- pages - contains the pages data
|   |-- page1.md
|   |-- page2.md
|   |-- folderA
|   |   `-- page3.md
|   |-- folderB
|   |   `-- page4.md
|   `-- index.md
`-- views - contains the templates (twig, nunjucks, ejs, mustache etc.)
    `-- main.twig
```

> Note: Reading the `messages` and `globals.yaml` needs a few additional lines of code. See the [example repository](https://github.com/staticpagesjs/example-site-cli).

For mid-sized projects it is a good idea to break apart more of this structure and use the following tree.

```
project
|-- build - will contain the produced output
|-- build.config.yaml - configuration for the staticpages cli
|-- build.lib.js - contains every user defined JS logic, except controllers
|-- controllers - controllers for the pagetypes
|   |-- pages.js
|   `-- news.js
|-- globals - additional context for the template renderer (twig, nunjucks)
|   |-- pages.yaml
|   `-- news.yaml
|-- messages - translation messages for the trans filter (twig, nunjucks)
|   |-- pages.de.yaml
|   |-- pages.fr.yaml
|   |-- pages.en.yaml
|   |-- news.de.yaml
|   |-- news.fr.yaml
|   `-- news.en.yaml
|-- pages - contains the pages data
|   |-- page1.md
|   |-- page2.md
|   |-- folderA
|   |   `-- page3.md
|   |-- folderB
|   |   `-- page4.md
|   `-- index.md
|-- news - contains the news data
|   |-- news-20201201.md
|   |-- news-20201202.md
|   `-- news-20201203.md
`-- views - contains the templates (twig, nunjucks, ejs, mustache etc.)
    |-- news.twig
    `-- main.twig
```

## Installing

Can be installed from NPM or pulling a pre-installed docker image from DockerHub.

```sh
$ npm i @static-pages/core @static-pages/cli @static-pages/markdown-reader @static-pages/twig-writer
```

> Note: Additional modules can be installed from the `@static-pages` namespace. Browse the [@static-pages](https://www.npmjs.com/org/static-pages) npm organization.

```sh
$ docker pull staticpages/cli
```

## Running the static pages generator

There is a JS API and a command line tool to use. The first gives more freedom and flexibility, the latter handles a few common cases for you.

### Using JS API

Create a .js file where you configure the static pages rendering.

```js
const { staticPages } = require('@static-pages/core');
const { markdownReader } = require('@static-pages/markdown-reader');
const { twigWriter } = require('@static-pages/twig-writer');

staticPages({
	from: markdownReader({
        cwd: 'pages',
        pattern: '**/*.md',
    }),
	to: twigWriter({
        view: 'main.twig',
        viewsDir: 'views',
		outDir: 'build',
        outFile: d => (d.url ?? d.header.path.replace(/\.md$/, '')) + '.html',
	}),
    controller: d => {
        d.now = new Date().toJSON();
        return d;
    },
}).catch(err => {
    console.error(err?.message ?? err);
    process.exit(1);
});
```

### Using the cli tool

Using the cli tool you have the option to store the configuration in a JSON or YAML file or alternatively you can specify every option as a cli argument.

> TODO: extend this section

### Docker image

> TODO: extend

On Linux/Mac:
```sh
$ docker run -it --rm -v "$(pwd):/project" staticpages/cli staticpages --config build.config.yaml
```

On Windows:
```sh
> docker run -it --rm -v "%cd%:/project" staticpages/cli staticpages --config build.config.yaml
```

> TODO: extend

On Linux/Mac:
```sh
$ docker run -it --rm -v "$(pwd):/project" staticpages/cli chokidar "**/*.md" "**/*.yaml" "**/*.js" "**/*.twig" -c "staticpages --config build.config.yaml"
```

On Windows:
```sh
> docker run -it --rm -v "%cd%:/project" staticpages/cli chokidar "**/*.md" "**/*.yaml" "**/*.js" "**/*.twig" -c "staticpages --config build.config.yaml"
```
