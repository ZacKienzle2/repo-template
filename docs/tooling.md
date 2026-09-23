# Tooling

Every check this repository runs, with the version it is pinned at and where it
comes from. The table is generated: the hooks are read from
`.pre-commit-config.yaml` and the actions from `.github/workflows` by the Cog
block below, through ruamel.yaml, whose round-trip loader keeps the version
comment a pinned action carries. The tooling-index hook refuses a commit in
which the table no longer matches those files, and copier regenerates it after
every render. `uvx --from cogapp --with ruamel.yaml cog -rU docs/tooling.md`
regenerates it by hand. A hook's source is the repository the hook configuration
names, where its documentation is; a local hook has none, and its entry in
`.pre-commit-config.yaml` says what it runs.

<!-- [[[cog
from pathlib import Path

from ruamel.yaml import YAML

yaml = YAML()
rows = []
for repo in yaml.load(Path(".pre-commit-config.yaml").read_text(encoding="utf-8"))["repos"]:
    source = "" if repo["repo"] == "local" else f"<{repo['repo']}>"
    for hook in repo["hooks"]:
        rows.append((hook["id"], str(repo.get("rev", "")), source))
actions = {}
for path in sorted(Path(".github/workflows").glob("*.yml")):
    for job in yaml.load(path.read_text(encoding="utf-8"))["jobs"].values():
        for step in [job] + job.get("steps", []):
            if "uses" not in step or "@" not in step["uses"]:
                continue
            reference, _, digest = step["uses"].partition("@")
            owner, name = reference.split("/")[:2]
            comment = step.ca.items.get("uses")
            version = (comment[2].value.split("\n")[0].strip("# ") if comment and comment[2] else "") or digest
            actions[f"{owner}/{name}"] = (version, f"<https://github.com/{owner}/{name}>")
rows.extend((action, version, url) for action, (version, url) in sorted(actions.items()))
table = [("Tool", "Version", "Source"), *rows]
widths = [max(len(row[column]) for row in table) for column in range(3)]

def line(row):
    return "| " + " | ".join(cell.ljust(width) for cell, width in zip(row, widths)) + " |"

cog.outl("")
cog.outl(line(table[0]))
cog.outl("| " + " | ".join("-" * width for width in widths) + " |")
for row in table[1:]:
    cog.outl(line(row))
cog.outl("")
]]] -->

| Tool                                 | Version     | Source                                                         |
| ------------------------------------ | ----------- | -------------------------------------------------------------- |
| check-added-large-files              | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-case-conflict                  | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-executables-have-shebangs      | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-illegal-windows-names          | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-merge-conflict                 | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-shebang-scripts-are-executable | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-symlinks                       | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-vcs-permalinks                 | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| check-xml                            | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| destroyed-symlinks                   | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| forbid-submodules                    | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| mixed-line-ending                    | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| trailing-whitespace                  | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| end-of-file-fixer                    | v6.0.0      | <https://github.com/pre-commit/pre-commit-hooks>               |
| prettier                             | v3.9.6      | <https://github.com/rbubley/mirrors-prettier>                  |
| prettier                             | v3.9.6      | <https://github.com/rbubley/mirrors-prettier>                  |
| taplo-format                         | v0.9.3      | <https://github.com/ComPWA/taplo-pre-commit>                   |
| disallow-caps                        |             |                                                                |
| check-dependabot                     | 0.38.0      | <https://github.com/python-jsonschema/check-jsonschema>        |
| check-github-workflows               | 0.38.0      | <https://github.com/python-jsonschema/check-jsonschema>        |
| check-github-workflows               | 0.38.0      | <https://github.com/python-jsonschema/check-jsonschema>        |
| zizmor                               | v1.30.0     | <https://github.com/zizmorcore/zizmor-pre-commit>              |
| zizmor                               | v1.30.0     | <https://github.com/zizmorcore/zizmor-pre-commit>              |
| actionlint                           | v1.7.12     | <https://github.com/rhysd/actionlint>                          |
| actionlint                           | v1.7.12     | <https://github.com/rhysd/actionlint>                          |
| yamllint                             | v1.38.0     | <https://github.com/adrienverge/yamllint>                      |
| yamllint                             | v1.38.0     | <https://github.com/adrienverge/yamllint>                      |
| validate-config-source               |             |                                                                |
| markdownlint-cli2                    | v0.23.2     | <https://github.com/DavidAnson/markdownlint-cli2>              |
| tooling-index                        |             |                                                                |
| shfmt                                | v3.13.1-1   | <https://github.com/scop/pre-commit-shfmt>                     |
| shellcheck                           | v0.11.0.1-1 | <https://github.com/shellcheck-py/shellcheck-py>               |
| editorconfig-checker                 | v4.0.1      | <https://github.com/editorconfig-checker/editorconfig-checker> |
| typos                                | v1.50.1     | <https://github.com/crate-ci/typos>                            |
| gitleaks                             | v8.30.1     | <https://github.com/gitleaks/gitleaks>                         |
| commitlint                           | v9.26.0     | <https://github.com/alessandrojcm/commitlint-pre-commit-hook>  |
| ascii-commit-message                 |             |                                                                |
| vale                                 | v3.20.0     | <https://github.com/errata-ai/vale>                            |
| vale                                 | v3.20.0     | <https://github.com/errata-ai/vale>                            |
| vale                                 | v3.20.0     | <https://github.com/errata-ai/vale>                            |
| actions/attest-build-provenance      | v1.4.4      | <https://github.com/actions/attest-build-provenance>           |
| actions/checkout                     | v7.0.1      | <https://github.com/actions/checkout>                          |
| actions/dependency-review-action     | v4.9.0      | <https://github.com/actions/dependency-review-action>          |
| actions/first-interaction            | v3.1.0      | <https://github.com/actions/first-interaction>                 |
| actions/setup-python                 | v7.0.0      | <https://github.com/actions/setup-python>                      |
| actions/stale                        | v9.1.0      | <https://github.com/actions/stale>                             |
| actions/upload-artifact              | v4.6.2      | <https://github.com/actions/upload-artifact>                   |
| amannn/action-semantic-pull-request  | v6.1.1      | <https://github.com/amannn/action-semantic-pull-request>       |
| anchore/sbom-action                  | v0.24.2     | <https://github.com/anchore/sbom-action>                       |
| astral-sh/setup-uv                   | v10.2.0     | <https://github.com/astral-sh/setup-uv>                        |
| carvel-dev/setup-action              | v2.2.0      | <https://github.com/carvel-dev/setup-action>                   |
| crazy-max/ghaction-github-labeler    | v6.0.0      | <https://github.com/crazy-max/ghaction-github-labeler>         |
| dessant/lock-threads                 | v6.0.2      | <https://github.com/dessant/lock-threads>                      |
| github/codeql-action                 | v4.38.1     | <https://github.com/github/codeql-action>                      |
| gitleaks/gitleaks-action             | v3.0.0      | <https://github.com/gitleaks/gitleaks-action>                  |
| google/osv-scanner-action            | v2.6.0      | <https://github.com/google/osv-scanner-action>                 |
| kentaro-m/auto-assign-action         | v2.0.2      | <https://github.com/kentaro-m/auto-assign-action>              |
| lycheeverse/lychee-action            | v2.9.0      | <https://github.com/lycheeverse/lychee-action>                 |
| ossf/scorecard-action                | v2.4.4      | <https://github.com/ossf/scorecard-action>                     |
| peter-evans/create-pull-request      | v8.1.1      | <https://github.com/peter-evans/create-pull-request>           |
| pre-commit/action                    | v3.0.1      | <https://github.com/pre-commit/action>                         |
| re-actors/alls-green                 | v1.3.0      | <https://github.com/re-actors/alls-green>                      |
| release-drafter/release-drafter      | v6.4.0      | <https://github.com/release-drafter/release-drafter>           |
| step-security/harden-runner          | v2.21.1     | <https://github.com/step-security/harden-runner>               |
| suzuki-shunsuke/pinact-action        | v3.0.0      | <https://github.com/suzuki-shunsuke/pinact-action>             |
| wagoid/commitlint-github-action      | v6.2.1      | <https://github.com/wagoid/commitlint-github-action>           |
| zizmorcore/zizmor-action             | v0.6.4      | <https://github.com/zizmorcore/zizmor-action>                  |

<!-- [[[end]]] -->
