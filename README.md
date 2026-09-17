# Bend 2 for Nix

Standalone Nix packaging for [Bend 2](https://github.com/bendlang/bend).
This repository contains only the flake, its lockfile, and this README. Bend's
source is fetched as a pinned, non-flake input; no changes to Bend are required.

## Install

Enable Nix's `nix-command` and `flakes` experimental features, then:

```sh
nix profile install github:nicolas-abril/bend2-nix
bend --version
bend main.bend
bend main.bend -o main
```

Newer Nix versions also spell the install command `nix profile add`.
To try Bend without installing it:

```sh
nix run github:nicolas-abril/bend2-nix -- --version
nix run github:nicolas-abril/bend2-nix -- main.bend
```

The package includes Bun, Clang, the prelude, native/JS effects, and the guide.
It exposes `bend` on ARM64 and x86-64 Linux and macOS. Set `CC` only to a
compatible Clang compiler; a caller's explicit `CC` is respected.

## Use from another repository

Put this in your project's `flake.nix`:

```nix
{
  inputs.bend.url = "github:nicolas-abril/bend2-nix";
  outputs = { bend, ... }: {
    devShells = bend.devShells;
  };
}
```

Add the flake to Git, run `nix develop`, and commit the generated `flake.lock`.
This provides Bend, Bun, Clang, Make, and Linux X11/ALSA development libraries.
It also sets `BEND_MAIN` and `BEND2_CORE` to the packaged compiler, for the SVG
and BendQuest demo build scripts. No sibling source checkout is required.

Custom shells, NixOS configurations, and other derivations can use
`bend.packages.${system}.default` (also named `bend`). Native compilation needs
Clang; set `CC=clang` when the surrounding environment otherwise selects GCC.
Add any app-specific libraries to that environment's build inputs.

GPU execution still needs compatible host hardware and drivers. Linux's CUDA
backend currently detects a toolkit at `/usr/local/cuda`; this flake does not
install CUDA. Native graphical/audio apps require their platform dependencies.

## Maintain

```sh
nix flake update bend2       # update just the pinned Bend source
nix flake update nixpkgs     # update the toolchain
nix flake check              # run, emit JS, and compile/run a CPU program
nix build                   # result/bin/bend
```

The package checks exercise a Bend test from an independent build directory,
including prelude/effect discovery and `bend guide`. Nix syntax and the source
layout's JS/native smoke tests were checked on macOS; a full Nix build has not
been validated yet.
