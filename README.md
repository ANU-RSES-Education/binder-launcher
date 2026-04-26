# ANU-RSES Binder launcher

A near-empty repository whose only job is to make
[mybinder.org](https://mybinder.org) launch a pre-built Docker image fast,
without re-cloning any large content repository each time.

## Why this exists

mybinder.org caches built images by **the launcher repo's commit hash**, then
clones whatever repo you point it at on every launch. If you point Binder
straight at a course repository, every launch re-clones hundreds of MB and
re-runs the (small) launcher build context.

This launcher repo is a few KB, so the launcher build context is trivial. The
heavy lifting — Python env, pyvista / cartopy / obspy / VTK / Mesa, jovyan
user, `jupyter lab` entrypoint — lives in a single image at
`ghcr.io/anu-rses-education/emsc-2022:latest`, pulled by `.binder/Dockerfile`.

Course content is fetched **at launch time** by
[`nbgitpuller`](https://nbgitpuller.readthedocs.io/), which is bundled in
the image. Editing a notebook in a course repo therefore needs no image
rebuild and no launcher rebuild — the next student to launch gets the
current content.

## Layout

```
.
└── .binder/
    └── Dockerfile     # `FROM ghcr.io/anu-rses-education/emsc-2022:latest`
```

That is the entire interesting content of this repository.

## Launching a specific course's notebooks

Build the URL using the [nbgitpuller link generator](https://nbgitpuller.readthedocs.io/en/latest/link.html):

| Field | Value |
|---|---|
| BinderHub URL | `https://mybinder.org/v2/gh/ANU-RSES-Education/binder-launcher/main` |
| Git repository URL | `https://github.com/<OWNER>/<COURSE-REPO>` |
| Branch | `master` (or `main`) |
| Application to open | JupyterLab |
| File to open | `Notebooks/...` (or wherever the entry-point notebook lives) |

Example launch URL pattern (decoded form):

```
https://mybinder.org/v2/gh/ANU-RSES-Education/binder-launcher/main
?urlpath=git-pull
  ?repo=https://github.com/ANU-RSES-Education/EMSC-2022
   &urlpath=lab/tree/EMSC-2022/Notebooks/LAB-week8/LAB8-Gutenberg-Richter.ipynb
   &branch=master
```

…with the inner `?` and `&` percent-encoded inside the outer `urlpath=` value.

## Image source

The image is built and pushed by
[`ANU-RSES-Education/EMSC-2022 / .github/workflows/build-binder-image.yml`](https://github.com/ANU-RSES-Education/EMSC-2022/blob/master/.github/workflows/build-binder-image.yml)
from
[`.binder/Dockerfile.source`](https://github.com/ANU-RSES-Education/EMSC-2022/blob/master/.binder/Dockerfile.source)
in that repo. The image name is historical (it was built first for EMSC-2022)
but the contents are course-neutral. Move the Dockerfile.source +
build workflow into a dedicated repo when this image starts being shared
with materially different courses.
