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

cog.outl(line(table[0]))
cog.outl("| " + " | ".join("-" * width for width in widths) + " |")
for row in table[1:]:
    cog.outl(line(row))
]]] -->
<!-- [[[end]]] -->
