---
layout: page
title: NPM vs PNPM vs Yarn
navigation_source: docs_nav
---

NPM is the popular package standard for JavaScript today. There are three separate package manager tools that you can use to install NPM packages.

- [NPM](https://docs.npmjs.com/getting-started/what-is-npm): The original implementation from NPM, Inc.  NPM works great for small projects, but there are known reliability issues for large scale development.  NPM 4.5.0 still seems to be more stable than 5.x and 6.x.

- [Yarn](https://yarnpkg.org/): A complete rewrite of NPM based around stability, security, and speed.  Yarn adds some cool additional features (e.g. workspaces, resolutions, etc), but maintains the same basic design.

- [PNPM](https://pnpm.js.org/): A redesign of the node_modules folder that eliminates phantom dependencies and  doppelganger installs.  Because it creates symlinks instead of copying files, PNPM is extremely fast, and uses the least disk space.

Rush is not a package manager.  It is a build orchestrator that in theory can use any package manager, although today we only support **NPM** and **PNPM**.  (We'd welcome a PR to add Yarn support.  The main work would be a reader/writer for Yarn's "lock file" file format, since it uses a proprietary format instead of JSON or YAML.)

## Which package manager should I use with Rush?

We recommend to use PNPM.  However, PNPM is not 100% compatible with NPM, because of (1) legacy packages that don't handle symlinks correctly when doing node module resolution, and (2) packages with phantom dependencies.  Some migration work is often needed to get PNPM working in a repo.  Either you need to fix the bad packages, or you need to add fixups in **common/config/rush/pnpmfile.js**.

If you encounter compatibility problems and aren't able to resolve them easily, then we recommend to use NPM.  If you encounter stability issues, try downgrading to NPM 4.5.0.

## What are "phantom dependencies"?

In computer science, package dependencies are modeled as a directed acyclic graph.  NPM's installation model maps this graph onto a tree of **node_modules** folders.  The module resolution algorithm seeks to find.


## What are "doppelgangers"?




There are three main package managers for the NPM registry. The original one, written by NPM Inc was [NPM](https://docs.npmjs.com/getting-started/what-is-npm). It is the most widely used package manager, but more recently, other package managers have been gaining more and more adoption. One such package manager is Yarn, which was created by Facebook. Yarn is more-or-less "npm plus" some extra functionality. It is also more performant that NPM, but because it has important model differences, we haven't added support for it yet, as this would be a non-trivial amount of work. Another alternative package manager is [PNPM](https://pnpm.js.org/). PNPM is much more performant and wastes much less disk space due to its fundamentally better design. We have recently added support in Rush for using PNPM, as we think it's improved design is superior to both NPM and Yarn.

## Which one should I use?
### NPM
NPM was the original package manager used by Rush. While it is likely the most stable package manager, it is also by far the least performance in terms of speed and disk usage. Make sure to use NPM 4, as NPM 5 has had a large number of regressions and is not fully supported by Rush.

### PNPM
PNPM is the exciting new one that you may want to try, it has improved architecture. PNPM has several advantages over NPM, including performance, disk efficiency, rigor and simplicity. The primary idea behind PNPM is to install packages a single time, and construct your node_modules folder using only symlinks. By comparison, NPM imposes a tree of physical folders that often requires excessive duplication of the exact same contents.

PNPM has a few other benefits:
* PNPM eliminates the annoying [race condition issue](https://github.com/request/request/issues/2807) in NPM!
* **Disk efficiency** - unlike NPM, PNPM will install a specific version of a package only once on disk, saving many gigabytes (reducing node_modules folder size from 10-30%).
* **Performance** – since PNPM only install packages a single time, then constructs the dependency graph using much cheaper symlinks, it is also much faster than PNPM. This in turn makes rush install much faster.
* With PNPM, “rush generate” is much faster because we no longer have to delete the node_modules folder and do a full re-install.
* **Rigor** – PNPM creates links in the node_modules folder *only* for direct dependencies. This means you can’t accidentally `require()` things that aren’t in your package.json, which can lead to strange errors for consumers of your library.
* **Simpler structure** – this will enable to more easily implement Rush feature requests such as repo-to-repo linking.

## How to tell Rush which package manager to use
Switching between PNPM and NPM is fairly trivial. Simply open your `rush.json` file, and either set the `npmVersion` or `pnpmVersion` field to whichever version of the package manager you are interested in doing.

For example, this repository is using PNPM, and the `rush.json` file looks like [this](https://github.com/Microsoft/web-build-tools/blob/master/rush.json#L2):
```
{
  "pnpmVersion": "1.43.1",
  ...
}
```

