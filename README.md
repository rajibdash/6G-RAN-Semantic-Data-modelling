# 6G RAN Semantic Data modelling

**Work in progress.** Exploring how to model and integrate a Semantic Layer and
a Knowledge Plane alongside the data and control planes for 6G Radio Access
Networks (RAN), including semantic-aware MAC scheduling.

## Repository structure

```text
.
|-- README.md                 # Project overview and navigation
|-- LICENSE                   # Project license
|-- .gitignore
|-- docs/
|   `-- design.md             # Architecture, scheduler design, and draft code
`-- references/
    `-- papers/               # Research PDFs
        |-- 2407.11161v2.pdf
        |-- 2512.20917v1.pdf
        `-- 6G and ORAN Semantic Survey.pdf
```

This is an initial organization of the existing material. Design notes and
embedded PyTorch blueprints remain together in the design document; they are
draft examples, not a packaged implementation.

## Design documentation

- [Background and motivation](docs/design.md#background)
- [High-level architecture and stack](docs/design.md#architecture-and-stack-high-level)
- [Semantic-aware MAC scheduler design](docs/design.md#semantic-aware-mac-scheduler-design-details)
- [HARQ, DTX, and carrier aggregation constraints](docs/design.md#semantic-aware-mac-scheduler-with-harq-dtx-and-carrier-aggregation-constraints)
- [Bibliography and external references](docs/design.md#references)

## Research papers

- [arXiv 2407.11161v2](references/papers/2407.11161v2.pdf)
- [arXiv 2512.20917v1](references/papers/2512.20917v1.pdf)
- [6G and ORAN Semantic Survey](references/papers/6G%20and%20ORAN%20Semantic%20Survey.pdf)

## Adding material

Keep design notes and architectural documentation in `docs/`, research PDFs in
`references/papers/`, and project-level information at the repository root.
Link new material from this README so it remains discoverable.
