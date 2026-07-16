# Version Control and Project Organization in Unity — Best Practice Guide (2020 LTS Edition)

> Nota de proceso (2026-07-10): transcripción en Markdown del e-book oficial de Unity Technologies (2022), a partir del PDF `2022_ABMVersionControlandProjectOrganizationinUnity_EBook_Final.pdf` aportado por Fere. Se conserva en inglés (idioma original) por fidelidad. Se omiten capturas de pantalla e imágenes decorativas a petición expresa; el texto y las tablas se conservan íntegros. Guardado como material de referencia para `documentation/`, consultado en el contexto de M00.F02 (T04, estrategia de ramas).

Source: Unity Technologies, © 2022. Original guide covers Plastic SCM, Git, and Perforce Helix Core.

## Contents

1. Introduction
2. Source control vs version control
3. Foundational concepts
4. Best practices for organizing a Unity project
5. Version control systems (Git, Perforce, Apache Subversion, Plastic SCM, comparison)
6. Setting up Unity to work with version control
7. Best practices for version control
8. Summary
9. Additional resources

---

## 1. Introduction

Software development becomes a different beast when you move from working on your own to working with a team. Source control, or version control, can be a daunting topic for game developers and creators, especially if you're not from a technical background. This guide explains the key concepts of version control, compares Plastic SCM, Git, and Perforce Helix Core, and provides tips for setting up a Unity project for smooth, efficient team collaboration.

## 2. Source control vs version control

Historically, "source control" (or SCM, source code management) described systems managing pure code. As Unity projects grew to include 3D models, textures, materials, and audio, "version control system" (VCS) became the more accurate term, since these tools now handle far more than text file changes. Both terms are used interchangeably, but "version control" / "VCS" is the more accurate label for Unity projects with large binary assets.

## 3. Foundational concepts

### How version control works

Project files are stored in a shared database called a repository ("repo"). You back up your project at regular intervals, and if something goes wrong, you can revert to an earlier version. Multiple individual changes are grouped and "committed" as a single group. With access to a project's entire history, it's easier to identify which changes introduced bugs, restore removed features, and document changes between releases.

### Why use version control?

Beyond backups, version control is useful for experimental changes: work on a new feature locally, and revert if it doesn't work out. It also protects against accidentally overwriting a teammate's work — you "pull" the latest updates before committing, which surfaces the dreaded "merge conflict" (usually resolvable once you understand the tools).

### Centralized vs distributed version control

**Centralized.** The repo lives on a server (on-site or cloud); users fetch from and send changes directly to it ("updating" and "committing"). Users can lock files to avoid conflicts ("checking out"). A user only ever has the latest version locally; the server holds the entire history.

**Distributed.** The repo lives in one place (e.g., GitHub), but users clone the *entire project history* to their workstation. Users commit locally without needing a connection, then `push`/`pull` to synchronize. This encourages committing little and often. File locking is less common since merges are easier to handle. Downsides: the full history on every machine takes a lot of space (Git LFS mitigates this for certain files), and developers can drift into isolation, making eventual merges harder.

Three systems covered in this guide and their model:

- **Git** — distributed
- **Perforce** — centralized
- **Plastic SCM** — both (configurable)

### Typical workflow

**Centralized:** (1) update working copy, (2) make changes, (3) commit to central server.

**Distributed:** (1) pull remote changes, (2) make changes, (3) commit, (4) repeat 2-3 as needed, (5) push all commits to the remote repo.

### Key terms

| Term | Explanation |
|---|---|
| Working copy | Your local version of the project (checkout/workspace). |
| Commit / check in | Encodes file modifications; sent to the server (centralized) or added to a changeset to push later (distributed). |
| Pull / update / check out | Retrieves the latest changes from the server. |
| Locking | Prevents a file from being edited by another user while you work on it. Generally unsupported in distributed workflows. |
| Clone | Getting a full copy of the project and its history onto your local machine (distributed workflow). |
| Tags | Notes attached to a commit, often marking a build point. |
| Branch | A parallel copy of the codeline, for isolated work (e.g., a new feature) without affecting the main line. |
| Merge | Combining two changesets into a new working copy — when a branch finishes, or when two people edit around the same time. Most merges are automatic. |
| Conflict | What happens when a merge can't be handled automatically — usually two people changing the same lines of code or the same binary file. Binary conflicts (scenes, Prefabs) are trickier than text conflicts. |
| Pull request | Signals that work on a branch is complete and ready to merge; gives leads/seniors a chance to review before accepting. |
| Head | The latest commit on your working copy. |
| Reset / revert | Discards local changes back to the state at head. |
| Index | (Git) describes current changes in your working copy, sitting in the staging area before a commit. |
| Git stash | Temporarily saves unfinished changes so you can switch to other work, then restores them later. |

## 4. Best practices for organizing a Unity project

### Project organization / folder structure

General recommendations:

- Document your naming conventions and folder structure (style guide / project template); make sure everyone follows it.
- Be consistent — if you must change naming rules, rename all affected assets at once (script it if there are many).
- Don't use spaces in file/folder names (Unity's CLI tools have issues with spaces) — use CamelCase instead.
- Separate testing/sandbox areas from production scenes; subfolders per username can divide work areas.
- Avoid extra folders at the project root — keep content inside `Assets/` unless there's a strong reason not to.
- Keep internal assets separate from third-party ones (Asset Store items, plug-ins) so updates don't collide with your own work.

> Note: if you modify a third-party asset/plug-in, version control helps you diff against a later update to find and reimplement your modifications.

Two example folder layouts given in the guide (asset-type based):

```
Example 1
Assets
+---Art
|   +---Materials
|   +---Models
|   +---Textures
+---Audio
|   +---Music
|   \---Sound
+---Code
|   +---Scripts   # C# scripts
|   \---Shaders   # Shader files and shader graphs
+---Docs          # Wiki, concept art, marketing material
+---Level         # Anything related to game design in Unity
    +---Prefabs
    +---Scenes
    \---UI
```

```
Example 2
+---Art
|   +---Materials
|   +---Models
|   +---Music
|   +---Prefabs
|   +---Sound
|   +---Textures
|   +---UI
+---Levels
+---Src
    +---Framework
    \---Shaders
```

Unity Hub templates split subfolders by asset type. Common ones:

| Asset Type | Explanation |
|---|---|
| Animations | Animated motion clips and controllers; Timeline assets; procedural rigging info. |
| Audio | Audio clips and mixers. |
| Editor | Editor-only tooling, not included in target builds. |
| Fonts | Fonts used in the game. |
| Materials | Surface shading properties. |
| Meshes | Models created in an external DCC application. |
| Particles | Particle System / Visual Effect Graph simulations. |
| Prefabs | Reusable GameObjects with prebuilt components. |
| Scripts | User-developed gameplay code. |
| Settings | Render pipeline settings (HDRP, URP, etc.). |
| Shaders | GPU programs. |
| Scenes | Functional portions of the project, often levels or parts of levels. |
| Textures | Texture files, UI overlay elements, lightmaps. |
| ThirdParty | External assets (Asset Store, etc.) kept separate — may have a fixed structure you can't alter. |

A good project structure from the start avoids version control issues later: **moving an asset between folders is often seen by a VCS as delete + add, losing the file's history**, unless the move happens through the Unity Editor (so the `.meta` file travels with it) and the VCS supports move-tracking (Plastic SCM explicitly does).

An Editor script is provided in the guide to auto-generate a standard folder set under `Assets/PROJECT_NAME/` via a menu item (`Assets > Create Default Folders`), keeping your own work separate from third-party packages.

### Empty folders

Empty folders can be an issue in version control — only create folders you actually need. Git and Perforce ignore empty folders by default; a common workaround is a `.keep` placeholder file inside. Plastic SCM tracks directories as first-class entities with their own history, so it doesn't have this problem — but note the `.meta`-file mismatch risk described below still applies to Git/Perforce.

### The .meta file

Unity generates a `.meta` file for every asset, including folders. Unlike most auto-generated files, `.meta` files **should** be committed — they store import settings and the identifiers Unity uses to link assets. **"Visible Meta Files" must be turned on** in the Version Control project settings (unless using the built-in Plastic SCM/Perforce modes). With Git/Perforce, if a `.meta` file for an empty folder is committed but the folder itself isn't versioned, other users will end up with an orphan `.meta` file Unity then deletes.

### Naming standards

| Standard | Example |
|---|---|
| Use descriptive names, don't abbreviate. | `largeButton`, `LargeButton`, `leftButton` — NOT `lButton` |
| Use Camel case / Pascal case, avoid spaces. | `OutOfMemoryException`, `dateTimeFormat` — NOT `Outofmemoryexception` |
| Use underscores/hyphens sparingly. | Active states: `EnterButton_Active`; texture maps: `Foliage_Diffuse`; LOD: `Building_LOD1` |
| Use number suffixes only for real sequences. | `Node0`, `Node1`, `Node2` |
| Follow the design document's naming. | If the doc says `HighSpellTower`, use that exact spelling. |

### Workflow optimization

- **Split up your assets.** Large single scenes don't collaborate well — break levels into smaller scenes (load additively via `SceneManager.LoadSceneAsync` with `LoadSceneMode.Additive`). Break work into Prefabs where possible; Prefab diffs are easier to read than scene diffs, and editing a Prefab avoids touching a scene others are working in. Unity has a built-in YAML-based Smart Merge tool for scenes/Prefabs conflicts.
- **Presets.** Customize the default state of any Inspector component; save as an asset and reapply later. Useful for enforcing standards, creating GameObjects with defaults, associating a type with a default Preset (Project Settings > Preset Manager), and reusing manager-window settings across projects.

### Code standards

No universal rule set — decide as a team and stick with it. Namespaces help organize code and avoid collisions with third-party assets; mirror your folder structure to your namespaces. A standard header (purpose, date, author) in script templates helps preserve context that can otherwise get lost over a project's history.

Unity reads script templates from `%EDITOR_PATH%\Data\Resources\ScriptTemplates` (e.g., `81-C# Script-NewBehaviourScript.cs.txt` for the default MonoBehaviour). Project-specific overrides go in `Assets/ScriptTemplates/`. Two placeholder keywords are supported: `#SCRIPTNAME#` and `#NOTRIM#`; custom keywords can be implemented via an Editor script using `OnWillCreateAsset`. The guide includes a full `KeywordReplace` example script that stamps creation date, project name, and developer username into new script headers.

## 5. Version control systems

### Git

Open source, free, flexible, distributed — one of the most popular VCS, though it can be daunting for non-technical users as a CLI-first tool. Created in 2005 by Linus Torvalds for the Linux kernel. Popular GUI clients: **Fork**, **GitKraken**, **VS Code** (built-in source control + extensions), **Visual Studio** (with GitHub extension), **SourceTree** (Atlassian, free), **Sublime Merge** (fast diffs/code review).

Strong branching/merging, but historically weaker with large binary files — **Git LFS** addresses this by replacing large files with text pointers. Being distributed, the entire repo and history live on each developer's machine, making branch switching and history navigation very fast.

GitHub is a *hosting service* for Git repos (not Git itself); popular for its free tier and lack of custom server setup. Unity publishes its C# editor/engine code on GitHub. GitHub also offers GitHub Desktop and a "GitHub for Unity" package that brings Git tools into the Editor. Alternatives: Bitbucket (Atlassian), GitLab.

### Perforce (Helix Core)

Enterprise-level, centralized, used by large studios (EA, Ubisoft) partly because it handles large files exceptionally well. Free for teams up to 5 users / 20 workspaces; can also deploy to cloud (AWS, Azure). Has built-in Unity Editor integration.

### Apache Subversion (SVN)

Free, open-source, centralized (unlike Git), handles large binaries reasonably. Was popular pre-Git-LFS for Unity. Command-line based, needs a third-party GUI (e.g., SmartSVN). Weak point: branching/merging — merges (especially conflicts, even false ones) can take hours versus minutes in other systems.

### Plastic SCM

Flexible, supports both programmers and artists, excels with large repos/binary files. File-based *and* changeset-based, so you can download only the files you need rather than the whole project. Free Cloud Edition for teams up to 3 users (5GB storage). Includes **Gluon**, a simplified client for artists: pick files to check out (with locking), work, check back in — hides the more programmer-oriented concepts.

Notable features: visual **image diff** (including a "swipe" comparison mode), an interactive **Branch Explorer** visualizing true branch relationships, a built-in **code review** system, and **Mergebot** for automated merging once a branch passes review. Joined the Unity family in 2020 (tight Editor integration). Can run distributed, centralized, or **multi-site** (per-site local servers syncing to a central/cloud server) — useful for globally distributed teams.

### Comparison (qualitative, from the guide's feature matrix)

The original comparison is a visual checkmark table (Plastic / Git / Perforce / Subversion across flexibility, binaries, GUI, workflow, merge tooling, cloud, diff, enterprise support); the checkmarks didn't survive text extraction cleanly, so here is the prose summary instead: Plastic SCM and Perforce are rated strongest for huge binaries and enterprise support; Git is rated strongest for flexible distributed workflows, GUI ecosystem, and diffing code moved across files; Plastic SCM specifically stands out for merge tooling (renames, moved files, refactors) and artist-oriented workflow; Subversion trails on merge tooling and workflow features.

## 6. Setting up Unity to work with version control

### Editor project settings

- **Perforce Helix Core:** `Edit > Project Settings > Version Control`, set Mode to Perforce, fill in workspace/server details. Files become "Under Version Control" with check-out/lock/unlock/submit/revert options; submitting opens a changeset dialog for the commit message.
- **Plastic SCM:** built into the supported Editor versions (2019.4.32f1+, 2020.3.20f1+, 2021.1.25f1+, 2021.2.0b16+, 2022.1.0a12+). Enable via the Plastic SCM toolbar icon (connect Unity ID, join/create org, set up repo and workspace) or via `Edit > Project Settings > Version Control` (Unity 2020 LTS), Mode = PlasticSCM. Files can be added/checked out/reverted/checked in from the Editor; a Changeset window is available via `Window > Plastic SCM`.
- **Git and other solutions:** `Edit > Project Settings > Version Control`, select **Visible Meta Files**. No further Editor-side options — meta files must be visible for the VCS to detect them.

### What to ignore

Only commit what **cannot be regenerated**. For Unity: only `Assets/` and `ProjectSettings/` should be under version control — **never commit `Library/`** (Unity recreates it on launch if missing, and it can get very large).

- Perforce: explicitly add `Assets/` and `ProjectSettings/` to the depot.
- Plastic SCM: auto-selects the right folders/files when set up from the Editor; ignore rules live in `ignore.conf` at the project root.
- Git: requires a `.gitignore` file — many GUI clients or GitHub offer a template at repo creation, or download one directly.

Also avoid committing `.exe`/`.apk` build artifacts, and Gradle/Xcode projects generated from the Unity build (unless you deliberately commit those to their *own* separate repo as part of an automated build pipeline).

### Working with large files

Unity projects are dominated by binary assets (textures, models, Prefabs, audio, timelines), which are hard to diff and can't be described incrementally — a changed binary is rewritten in full on every commit. In a distributed VCS, the *entire history* of every version lives on each machine, which can consume large amounts of disk space over time. This is historically why centralized workflows (large binaries live only on the server) were preferred for asset-heavy teams.

Perforce and Plastic SCM (centralized mode) handle large files well natively. Plastic's **Dynamic Workspace** (virtual filesystem) downloads files on demand — you see everything, but not everything is actually downloaded locally. **Git**, being distributed, struggles more with large files by default — **Git LFS is recommended** whenever large files are involved, replacing them in `.git` with text pointers while the actual asset lives on a server (e.g., GitHub).

## 7. Best practices for version control

(These come from Unity's Enterprise Support Team, based on real-world studio projects.)

- **Commit little, commit often.** A commit should relate to one task/ticket (unless a single line fixes multiple bugs). Break large features into smaller tasks with their own commits. Smaller commits make it much easier to isolate and revert a bad change without losing unrelated good ones.
- **Keep commit messages clean.** Describe *what* changed in terms someone can search for later (e.g., "Added high score tables to the menu", not a joke or non-description). Referencing a ticket number (JIRA, GitLab) is even better — some setups support "smart commits" that change ticket status directly from the commit message (e.g., `JRA-123 #close #comment task completed`).
- **Avoid indiscriminate commits.** `git commit -a` (or equivalents) should really only be used for a project's very first commit. Otherwise, only stage files actually related to the change — Unity in particular can mark scenes/Prefabs/sprite atlases as changed even when you didn't intend to touch them, and an accidental commit to a file a teammate is working on creates unnecessary merge pain.
- **Get the latest.** Pull regularly to avoid working in isolation and reduce merge-conflict risk. Typical Git daily loop: `git pull` → (edit → `git commit` → `git pull`) as many times as needed → `git pull` once more → `git push`. Perforce equivalent: get latest → check out → edit → submit. Plastic SCM has centralized, distributed, and multi-site variants of the same idea (sync/pull → check out → edit → check in → sync/push).
- **Know your toolset.** Whatever VCS the team picks, everyone should be comfortable with its core loop. Git: not everyone needs the same GUI, but everyone should understand commit/pull/push and staging only what's needed. Plastic SCM: artists benefit from Gluon's simplified lock/checkout/submit flow. Perforce: use the built-in Unity Editor integration for asset check-out/check-in without leaving the Editor.
- **Feature branches and Git Flow.** For long-standing, multi-release projects, splitting work into branches instead of a single trunk/main line is hugely beneficial. **Git Flow** uses dedicated branches for features, bug fixes, and releases — a feature is developed in isolation and merged back when done, while a hotfix on a previous release can ship independently of in-progress feature work. Plastic SCM's equivalent is **task branches**: one short-lived branch per tracked task (if a task needs more than a handful of commits, it's probably too big and should be split). Perforce uses **Streams** (a stream depot type, with every non-mainline stream requiring a parent stream); switching/copying between streams only merges metadata for changed files, which keeps context switches fast.
- **Pull requests.** Good practice once a feature branch is complete: open a PR to signal readiness and let a senior developer / DevOps review before merging into the mainline. Plastic SCM automates merge-after-review via **Mergebot**; Perforce has **Helix Swarm** for code review, which can integrate automated testing.

## 8. Summary

The biggest takeaway: clear team communication and agreed guidelines (project structure, chosen VCS, workflow within that VCS) matter more than which specific tool you pick. Once that foundation is in place, integrating other tools (JIRA, GitLab, build automation, testing) becomes much easier. Even solo projects benefit from these same organizational and version-control principles.

## 9. Additional resources (as listed in the original guide)

- Eight factors to consider when choosing a version control system
- Introduction to version control, Unite Now 2020
- *Git Apprentice*, by Chris Belanger and Bhagat Singh
- Version Control for Games with Unity's Plastic SCM
- Plastic SCM product documentation
- Mergebot in Plastic SCM
- Unity open project with version control
- How KO_OP uses Plastic SCM to accelerate production
- The hidden productivity costs disrupting your release timelines
- Perforce setup / How to Configure Helix Core and Game Engine / Helix Core documentation
- Using external version control with Unity (SVN setup)
