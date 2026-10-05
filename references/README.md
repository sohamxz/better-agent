# Reference knowledge base

This directory maintains indexed, version-stamped technical reference documentation for the project.

## Directory structure

```text
references/
|-- docs/          # Verified SDK syntax, breaking changes, and version notes
|-- github/        # Upstream architecture patterns and open-source issue workarounds
`-- apis/          # Verified request and response payloads, headers, and status codes
```

## Operational function

1. Persistent Ground Truth: Distills external library research and upstream specifications into local markdown files.
2. Context Acceleration: Agents and subagents consult local reference files before performing external searches.
3. Continuous Maintenance: Updates documentation when API contracts drift or new edge cases are identified.
