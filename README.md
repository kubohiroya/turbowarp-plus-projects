# turbowarp-plus-projects

English | [日本語](README.ja.md)

The default address space for projects made with [TurboWarp Plus Editor](https://kubohiroya.github.io/turbowarp-plus-editor/), served by GitHub Pages at `https://kubohiroya.github.io/turbowarp-plus-projects/`.

No projects are published here yet. The repository exists so that the address space is claimed before anything is placed in it.

## Why it exists

A TurboWarp Plus project is identified by a URI, not by a fingerprint of its content. The URI is stored in the manifest inside the SB3 (`turboWarpPlus.uri`). It is both where the project can be downloaded and the project's name: the browser's extension cache and the opener's recorded decisions about extensions are filed under it. A content hash would change on every save and file the same project under a new name each time.

When the author has not chosen an address, the editor gives the project one on its first save:

```text
<editor origin>/turbowarp-plus-projects/{last 2 of uuid}/{last 4 of uuid}/{uuid}.sb3
```

- The **origin** is `location.origin` of the page the editor was opened from, without a path. The public Plus Editor is served from `https://kubohiroya.github.io`, so a project made there is addressed under `https://kubohiroya.github.io/turbowarp-plus-projects/…`, which is this repository's GitHub Pages site.
- The **uuid** is a UUID version 7, which sorts by the time of the first save.
- The **two directory levels** are taken from the end of the UUID. A version 7 UUID begins with the top of a millisecond clock, which barely moves, so sharding on its first characters would put every project in one directory.
- The address is deliberately opaque. One derived from the title would change when the title changed and file one project under a second name. A readable address belongs to the deliberate act of publishing, where the author names it.

Example:

```text
https://kubohiroya.github.io/turbowarp-plus-projects/66/66aa/0193f0a1-2b3c-7d4e-8f01-2233445566aa.sb3
```

The address is built by `./project-authoring` (`createProjectUUID`, `defaultProjectURI`) in [turbowarp-extension-loader](https://github.com/kubohiroya/turbowarp-extension-loader). The editor was wired to it in [turbowarp-plus-editor#55](https://github.com/kubohiroya/turbowarp-plus-editor/issues/55). The design is described under "作品を同定するもの" in [the editor architecture](https://github.com/kubohiroya/turbowarp-plus-packages/blob/main/docs/editor-architecture.ja.md) (Japanese).

An address works as a name even while nothing is served there. Without this repository, every address the public editor hands out would point at a place nobody owns.

## What is settled

- **An address that does not resolve says nothing.** A project that was saved but never published and one whose published copy has gone look exactly the same from outside: a 404. Never decide anything, and above all never delete anything, because an address is unreachable.
- **The address is the project's own claim and is not verified.** A project can claim any address. That only confuses the per-project accounting; the extension cache is keyed by digest and checked on every read.
- **Only an editor opened over HTTPS gives addresses.** Projects made in a preview on `http://localhost` or in an editor opened from a file get none and are filed in the shared bucket.
- **An editor served from another origin addresses projects under that origin.** A fork of the editor deployed elsewhere puts its projects under that host's `/turbowarp-plus-projects/`. This repository only covers projects made in the editor served from `kubohiroya.github.io`.
- **The download file name is unrelated to the address.** The name on disk comes from the project's title.

## Plan for using it

Nothing below is decided or implemented yet.

### 1. Placing a project

To publish a project, its SB3 is placed at the path its manifest already names:

```text
66/66aa/0193f0a1-2b3c-7d4e-8f01-2233445566aa.sb3
```

- The path comes from `turboWarpPlus.uri` inside the SB3. Placing it under another name would make the address the project claims differ from the one it is served at.
- Placing a new version at the same path updates the project. The address stays the same, so openers' records and the cache accounting carry on.
- Only the owner can write to this repository. Anyone using the public editor receives addresses here, but only projects the owner accepts are placed here. Authors who want to publish their own work on their own site should name their own address, for example on their own GitHub Pages.

The first step is for the owner to commit projects by hand or through pull requests. Automation comes after that, for example:

- publishing from the Git panel of Plus Editor
- copying from a release of a project repository made with [turbowarp-plus-editor-project-starter](https://github.com/kubohiroya/turbowarp-plus-editor-project-starter)

### 2. Checks on placement (planned)

Pull request CI is to check that:

- the path has the form `{2}/{4}/{uuid}.sb3` and the two directory levels match the end of the UUID
- the UUID is version 7
- the SB3's `turboWarpPlus.uri` equals the public URL of the place it is put
- the file is well below GitHub's limits. GitHub warns about files over 50 MB and rejects files over 100 MB. Files stored with Git LFS are not served by GitHub Pages, so LFS is not used.

### 3. An index (planned)

Because addresses are opaque, a list meant for people shows each project's title beside its address. The plan is to collect titles and addresses from the placed SB3 files and generate an index page at the top of this site.

### 4. Not deleting

A published address lives on as the project's name in other people's records. Placed projects are not deleted as a rule. Before withdrawing one, decide how it is handled (marked as withdrawn, or simply a 404) first.

### Open questions

- Where the publishing action lives: the Git panel, project-starter, manual commits, or a combination
- The license of placed projects, and whether projects by people other than the owner are accepted
- What happens here to the old address of a project that moved to an address of its own
- What to do as the repository grows: a GitHub Pages site is limited to 1 GB

## License

Each project placed here states its own license. A license for the repository as a whole will be chosen when projects start being placed here.
