# Notes

This repository demonstrates Chainguard hardened GitHub Actions. Every
workflow step that pulls in third-party code uses a hardened drop-in
replacement from the `chainguard-actions` organization, pinned to an
immutable commit SHA.

The actions chosen here are deliberately obscure — small community
actions are exactly the kind of dependency that rarely gets a security
review before being handed the keys to a CI environment.
