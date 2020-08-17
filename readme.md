# Yarn Not Pruning Scoped Projects Issue Example

This project is a demonstration of an issue with yarn not pruning (after running `install`) scoped packages correctly.

## How to reproduce the problem

1. `cd` into `testProject`
1. If you have run through these reproduction steps already, make sure you revert any changes to `packages.json`, delete the old `node_modules`, and `yarn.lock` file
1. Run `yarn install` to generate the `yarn.lock` file and initial `node_modules` tree

    this results in the following `node_modules` tree (at least on windows 10, yarn 1.22.4)

    - testProject
    - @some-scope/shared-dependency-x@1.0.0
    - @some-scope/shared-dependency-y@1.0.0
    - dependency-a@1.0.0
    - dependency-b@1.0.0
        - @some-scope/shared-dependency-x@1.5.0
        - @some-scope/shared-dependency-y@2.0.0

1. Run `yarn add file:../dependencyA/version2.0.0 file:../dependencyB/version2.0.0` to upgrade the dependencies in `testProject`, then run `yarn install --check-files` to clean up duplicate entries in the lock file

    This results in the following `node_modules` tree

    - testProject
    - @some-scope/shared-dependency-x@2.0.0
    - @some-scope/shared-dependency-y@1.0.0
    - dependency-a@2.0.0
    - dependency-b@2.0.0
        - @some-scope/shared-dependency-x@1.5.0 <-- **This should not be here**
        - @some-scope/shared-dependency-y@2.0.0

At this point you should be able to see the issue. There is an extra `@some-scope/shared-dependency-x@1.5.0` in `dependency-b`'s `node_modules` folder that should have been deleted during `yarn install`.
