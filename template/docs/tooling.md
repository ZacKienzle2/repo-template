# Tooling

Every check this repository runs, with the version it is pinned at and where it
comes from. The table is generated: the hooks are read from
`.pre-commit-config.yaml` and the actions from `.github/workflows` by the Cog
block below, and the tooling-index hook refuses a commit in which the table no
longer matches those files. `uvx cogapp -r docs/tooling.md` regenerates it, and
copier runs that after every render. A hook's source is the repository the hook
configuration names, where its documentation is; a local hook has none, and its
entry in `.pre-commit-config.yaml` says what it runs.

<!-- [[[cog
import re
from pathlib import Path

rows = []
repo = rev = ""
for line in Path(".pre-commit-config.yaml").read_text(encoding="utf-8").splitlines():
    if found := re.match(r"\s*- repo: (\S+)", line):
        repo, rev = found.group(1), ""
    elif found := re.match(r"\s*rev: \"?([^\"\s]+)", line):
        rev = found.group(1)
    elif found := re.match(r"\s*- id: (\S+)", line):
        rows.append((found.group(1), rev, "" if repo == "local" else repo))
actions = {}
for path in sorted(Path(".github/workflows").glob("*.yml")):
    text = path.read_text(encoding="utf-8")
    for action, version in re.findall(r"uses: ([\w.-]+/[\w.-]+)[\w./-]*@[0-9a-f]{40} # (\S+)", text):
        actions[action] = (version, "https://github.com/" + action)
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
