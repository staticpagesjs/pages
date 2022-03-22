# Integration with GitLab Pages

GitLab Pages can be used to host a website which is published from a git repository. With GitLab Runners it is easy to use the static-pages Docker image and automate the static website generation.

## Automating with GitLab Runners

The Static Pages JS project has a Docker image which can be used in a GitLab Runner build pipeline. The configuration below runs the commands in the `staticpages/cli` (latest) container.

```yaml
# .gitlab-ci.yml
stages:
  - build
build:
  image: staticpages/cli
  stage: build
  script:
    - node build.js
    - sass --no-source-map sass:build/assets
    - copyfiles -u1 public/** build
```

To enable incremental builds, enable caching in the configuration.

```yaml
# .gitlab-ci.yml
stages:
  - build
build:
  image: staticpages/cli
  stage: build
  cache:
    paths:
      - build/
  script:
    - node build.js
    - sass --no-source-map sass:build/assets
    - copyfiles -u1 public/** build
```
