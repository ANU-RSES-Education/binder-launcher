# This is the Dockerfile that mybinder.org reads when launching from this repo.
#
# It pulls a pre-built image from GHCR. The image is generic and shared
# across multiple ANU-RSES-Education courses; it carries the python /
# scientific / mapping / pyvista stack plus `nbgitpuller`. Per-course
# notebook content is fetched at launch time via nbgitpuller URLs (see
# README), so this image and this launcher repo never need to be rebuilt
# when a course's notebooks change.

FROM ghcr.io/anu-rses-education/binder-launcher:latest
