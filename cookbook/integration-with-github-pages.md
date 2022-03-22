# Integration with GitHub Pages

GitHub Pages can be used to host a website which is published from a git repository. By default `jekyll` used as the static pages generator for your GitHub Pages projects.

## Automating with GitHub Actions

The Static Pages JS project has a Docker image which can be used in a GitHub Actions build process. The configuration below runs the commands in the `staticpages/cli` (latest) container.

```yaml
# .github/workflows/staticpages.yaml
name: Generate website and deploy to GitHub Pages
on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    container: staticpages/cli
    steps:
      - uses: actions/checkout@v2
      - name: Generate static site
        run: |
          node build.js
          sass --no-source-map sass:build/assets
          copyfiles -u1 public/** build
          touch build/.nojekyll
      - name: Deploy with gh-pages
        run: |
          git remote set-url origin https://git:${GITHUB_TOKEN}@github.com/${GITHUB_REPOSITORY}.git
          gh-pages --dotfiles --dist build --user '${{ github.event.pusher.name }} <${{ github.event.pusher.email }}>'
        env:
          CACHE_DIR: .cache/gh-pages
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

To fine tune the build steps you can add or remove commands from the `Generate static site` stage.

1. `node build.js`\
	Starts the Static Pages compilation process. When using the CLI tool, this should be `staticpages --config my.config.yaml`.

2. `sass --no-source-map sass:build/assets`\
	Compiles all sass resources to css (from `sass` directory to `build/assets`).

3. `copyfiles -u1 public/** build`\
	Copies all static assets (like favicon, images etc.) to the build folder.

4. `touch build/.nojekyll`\
	Disables Jekyll, the default static pages generator of GitHub Pages.

Deployment is made to the `gh-pages` branch with the `gh-pages` tool.
