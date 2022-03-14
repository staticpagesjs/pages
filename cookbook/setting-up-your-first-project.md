# Setting up your first project

This project is unopinionated about almost everything; yet here are some recipes that are useful for starting your workflow. Default values for readers and writers are set to match these recipes.

## Directory strucutre

This structure is optimal for small sites, where only a few pagetypes defined.

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
`-- views - contains the templates for the renderers (twig, nunjucks, ejs, mustache etc.)
    `-- main.twig
```

For mid-sized projects it is a good idea to break apart more of this structure.

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
`-- views - contains the templates for the renderers (twig, nunjucks, ejs, mustache etc.)
    |-- news.twig
    `-- main.twig
```

## Running the static pages generator

There is a JS API and a command line tool to use. The first gives more freedom and flexibility, the latter handles a few common cases for you.

### Using JS API

Create a .js file where you configure the static pages rendering flow.

> TODO: extend this section

### Using the cli tool

Using the cli tool you have the option to store the configuration in a JSON or YAML file or alternatively you can specify every option as a cli argument.

> TODO: extend this section
