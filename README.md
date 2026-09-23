# repo-template

The lint, CI, release and documentation configuration five repositories share,
as a [copier](https://copier.readthedocs.io) template. Each repository pulls a
change through `copier update`, which its template-update workflow runs weekly.

```sh
uvx copier copy --trust gh:ZacKienzle2/repo-template .
```

The questions are in `copier.yml`, with what each answer renders in its help.
The sources are under `template/`, written with Jinja delimiters that keep each
YAML source a valid YAML file, so the same hooks that check a rendered file
check its source, pinact and `pre-commit autoupdate` move the pins in the
sources, and vendir fetches the upstream files the ignore and attributes files
assemble. This repository is itself a render of them: `.copier-answers.yml` at
the root records the answers, with `is_template` true, so the root is checked by
the same hooks and workflows as every repository it renders for.
`docs/tooling.md` indexes those hooks and workflows.
