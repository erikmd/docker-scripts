# docker-hub-helper

## Summary

This repo gathers a python3 program [dhh](./dhh),
to help maintain multi-branches, automated-build repos on Docker Hub.

## Installation

Put this program in the `PATH` and run: `dhh -h`

## Usage summary

```
usage: dhh [-h] [--version]
           {branches,reset,create,trigger,rebase,push,delete} ...

A tool to help maintain multi-branches, automated-build repos on Docker Hub.
Assume the considered Git repo has master as main branch, origin as remote.

positional arguments:
  {branches,reset,create,trigger,rebase,push,delete}
    branches            fetch, checkout and list remote branches
    reset               reset specified branches w.r.t. origin
    create              fetch and create a stable branch from [origin/]master
    trigger             trigger rebuild of branches
    rebase              fetch and rebase branches on master
    push                push modified branches to trigger rebuild
    delete              delete a local and remote branch

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit
```

The location of the local Git repo can be specified by an argument
`--repo …/path/to/git/repo`, that is accepted by all sub-commands
(except `trigger`).

## Examples

The `--repo REPO` argument is omitted in the examples below.

* To update a local clone:

```bash
dhh branches
dhh reset --all  # if need be
```

* To add a commit in `master` and rebase stable branches:

```bash
cd $repo
git checkout master
git commit -a -m "Do something"
dhh rebase --all
```

* To dockerize a new point release of Coq, e.g. the patchlevel `8.8.2`:

```bash
dhh branches
dhh create --from 8.8.1 -e COQ_VERSION=8.8.2 8.8.2
  # open Docker Hub's build settings
  # and replace "8.8.1" with "8.8.2"; then
dhh delete 8.8.1
dhh push -n
dhh push
```

* To dockerize a new beta release of Coq, e.g. the version `8.9+beta1`:

```bash
dhh branches
dhh create --from dev -e COQ_VERSION=8.9+beta1 8.9-beta1
  # open Docker Hub's build settings
  # and add "8.9-beta1"; then
dhh push -n
dhh push
```

* To dockerize a new stable release of Coq, e.g. the version `8.9.0`:

```bash
dhh branches
dhh create -e COQ_VERSION=8.9.0 8.9.0
  # open Docker Hub's build settings
  # and replace "8.9-beta1" with "8.9.0"; then
dhh delete 8.9-beta1
dhh push -n
dhh push
```

* To trigger the rebuild of `dev`:

```bash
dhh trigger -b dev coqorg/coq $token
```

## Author and License

This tool was written by [Érik Martin-Dorel](https://github.com/erikmd).
It is distributed under the
[BSD-3 license](https://opensource.org/licenses/BSD-3-Clause).
