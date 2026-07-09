# ComplexLab

Personal learning lab: visual Unity pieces about artificial life, consciousness, complex systems, and applied mathematics (complex numbers, fractals, chaos, geometry), managed with **Spec Driven Development (SDD)** and the Claude ecosystem (Chat, Cowork, Code).

The real goal is twofold: learn these concepts, and learn how to work with AI on a project run with professional rigor.

## Goals

1. Learn to work with AI in general and Claude in particular (tools, models, agents, skills).
2. Set up and oversee a complex project: planning, tracking, and testing.
3. Complete a no-code project following Spec Driven Development.
4. Learn about artificial life, consciousness, and the origin of life.
5. Learn applied mathematics: complex numbers, fractals, chaos theory, geometry.
6. Apply appropriate design patterns, especially Unity-specific ones.
7. Connect Claude with other tools (Jira, VS Code, Unity, Git, Blender).
8. Publish selected results on blog and LinkedIn.

## Structure

The whole `ComplexLab/` folder is a single Git repository (linked to GitHub `complexlab`) — code and documentation share one history.

```
ComplexLab/                  Git repository root
├── .gitignore              OS/editor exclusions (repo-wide)
├── .gitattributes          Git LFS patterns (binary assets and office documents)
├── documentation/          General project documentation
│   ├── PROJECT.md          Vision, goals, conventions, and methodology
│   ├── TOOLS-GUIDE.md      Guide for choosing Chat/Cowork/Code and models
│   ├── ROADMAP.md          Module roadmap
│   └── templates/          Templates (contract, module, dossier, conclusions)
├── dossiers/               Topic dossiers (one per assignment folder)
├── modules/
│   └── MXX/                One module per topic
│       ├── README.md       Module overview
│       └── features/
│           └── MXX.FYY/    Contract and feature-specific info
└── code/                   Unity project, with its own .gitignore (Unity-specific)
```

See `documentation/PROJECT.md` for the master document and `documentation/ROADMAP.md` for the module plan (M00 setup → M08 consciousness and cognition).

## Methodology

Nothing gets implemented without an approved contract (`contract.md`). Feature lifecycle:

`Draft → Spec-Approved → In-Progress → Testing → Integrated → Documented → Closed`

Full detail in `documentation/PROJECT.md`, section 5.

## Conventions

- **Languages:** documentation, contracts, and dossiers in Spanish; code, branches, and commits in English.
- **Naming:** modules `MXX`, features `MXX.FYY`, tasks `MXX.FYY.TZZ`, branches `feature/