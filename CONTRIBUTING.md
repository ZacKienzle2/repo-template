# Contributing

Contributions follow the [Code of Conduct](CODE_OF_CONDUCT.md).

## Setup

```sh
git clone https://github.com/ZacKienzle2/repo-template
cd repo-template
prek install
```

The hooks in [.pre-commit-config.yaml](.pre-commit-config.yaml) format and lint
every commit, and CI runs the same hooks.

## Commits

Messages follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/). The Commit Lint workflow checks each commit and the pull
request title.

## Releases

Pushing a `vMAJOR.MINOR.PATCH` tag publishes a release with GitHub's generated
notes.
