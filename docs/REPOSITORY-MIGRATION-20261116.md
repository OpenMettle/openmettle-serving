# Repository migration — 16 November 2026

The owner authorised the transfer of the serving project into OpenMettle and a
proper upstream SGLang fork. The serving repository retained its identity
(`1419544423`), Git history, issues and pull requests at
`OpenMettle/openmettle-serving`. Its source HEAD at transfer was
`8a53e9c2bf2837d4cd9296b18627067be98f27e8`.

## Source import

`OpenMettle/sglang` remains in the `sgl-project/sglang` fork network.
`openmettle/rdna4` starts at upstream `fcb3274646e43c16d70fd83806ebb3a5134d5e81` and contains
one commit for each of the existing 33 patches, in filename order. There was no
upstream upgrade and no patch rewrite. Each commit records its original patch,
SHA-256, last source edit and editor. Original authorship and upstream pull
request attribution remain in that source history; an import commit does not
claim authorship of the imported implementation.

| Identity | Value |
| --- | --- |
| Frozen patchset SHA-256 | `d9af3651b8857720c996ec27c4341437c1ef80a78cc49cf0eb14536a8565f9cd` |
| Exact source import commit | `35c3a9d4996f9205acbb726d29cbf92d0e03634a` |
| Exact imported source Git tree | `334f60f8c34ca63c9b10f2b5364631c17c69a51c` |
| Published fork commit, including notices | `2e9dc033cfc7886371dc71ef632848ae7a63fa52` |
| Published Git tree | `0113a997f5f01d6232cde76ef097060f43ece24d` |

[SGLANG-IMPORT-20261116.json](SGLANG-IMPORT-20261116.json) maps every patch to its
published commit and tree. [serving/sglang-source.json](../serving/sglang-source.json)
is the initial source pin for future build integration. The existing workstation
pipeline does not consume that new file yet.

## Verification performed

- The existing patchset hashing procedure reproduced the pinned SHA-256.
- All 33 patches applied with `git apply --check` and `git apply --index`.
- An independent fresh checkout used the existing build's `git apply --check`
  and `git apply` sequence. Its complete Git tree equalled the imported tree,
  including file modes and symlinks.
- Every uploaded patch commit returned the expected Git tree, and a fresh Git
  fetch of the published branch matched the local import.
- All 73 changed Python files passed syntax parsing during import.
- The final commit adds only six metadata and licence files; it changes no
  imported engine bytes. Documentation changes passed `git diff --check`.

These checks establish source equivalence, not runtime qualification. No image
was built, no GPU performance or quality test was run, and no deployment was
changed. Historical measurements remain attached to their original image and
profile. The dated records in this repository are preserved unchanged.

## Repository and branch boundaries

- **SGLang fork:** engine code and engine regression tests. `openmettle/rdna4`
  is the AMD working branch; `main` retains upstream history and remains the
  repository default. Feature PRs should explicitly target `openmettle/rdna4`.
- **Other architectures:** introduce an `openmettle/nvidia` or another branch
  when there are actual architecture changes to maintain. Keep common features
  portable and prefer sharing focused commits and contributing upstream.
- **Serving repository:** curated builds, dependency pins, model profiles,
  benchmark tooling and qualification evidence. Qwen is the first model family;
  this repository is not restricted to Qwen. Each versioned profile should bind
  weight revision, quantisation, backend image, architecture, resource budget,
  launch settings and validation evidence.
- **Deployment repository:** generic Kubernetes and Helm packaging. Different
  model profiles can run as separate workloads and scale independently without
  creating one source repository per model.

The fork retains the original serving MIT notice, AMD AITER MIT terms and FLA
MIT terms. Its `OPENMETTLE-NOTICES.md` also retains the Tri Dao attribution found
in the pinned upstream FLA-derived kernel. This supplements the earlier notice
record without rewriting it or claiming a complete dependency audit.

## Build transition

The existing workstation build still uses the original upstream pin plus the
frozen patch series. Keep that path reproducible until the replacement is
qualified. New engine development belongs in the fork; do not evolve both copies.

The next build integration must fetch the full fork commit, **skip patch
application**, record that commit and the dependency pins in image provenance,
and qualify the intended model profile before changing deployed image digests.
Do not merely replace `SGLANG_REPOSITORY` in the old pipeline: it still applies
all patches and derives package versions from the old source identity.

## Developer checkout

For an existing serving checkout:

```sh
git remote set-url origin https://github.com/OpenMettle/openmettle-serving.git
git fetch origin
```

For the engine, alongside the other OpenMettle repositories:

```sh
git clone --branch openmettle/rdna4 https://github.com/OpenMettle/sglang.git
git -C sglang remote add upstream https://github.com/sgl-project/sglang.git
```

Read the fork's `AGENTS.md` and `OPENMETTLE.md`. The working set now includes
`openmettle-core`, `openmettle-app`, `openmettle-deploy`, `openmettle-serving`
and `sglang`, each retaining its own Git root.
