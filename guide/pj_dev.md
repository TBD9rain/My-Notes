# Introduction

**Current Contents:**
- **Version controlling**
- **File organizing**


# Project Version Controlling

[Git](https://git-scm.com/) is a convenient version controlling tool.


## Git Tips

Atomic commit shall be the first choice for Git.
Atomic commit signifies that a commit should be the smallest complete unit of changes for one aim.

A root commit with nothing but a '.gitignore' file might help.

`git rebase` should be considered when facing git error correcting or similar issues.
Project error should be fixed with another commit with corresponding messages.

Hierarchical '.gitignore' files might help with clear file management.

## Git Commit Message

Git commit message format is referenced from
[Conventional Commits](https://github.com/conventional-commits/conventionalcommits.org/blob/master/content/v1.0.0/index.md)
and [angular](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines).

```
<type>[(<scope>)][!]: <description>

[<body>]

[<footer>]
```

`<type>` must be one of following:
- chore, changes not directly affect main design
- docs, changes of documentations
- feat, changes for a new feature
- fix, changes to fix a bug
- perf, changes to improve performance
- refactor, changes of codes structure not affecting functions
- revert, revert changes with `git revert`
- style, changes about formatting code style
- test, changes of verification or validation

For example, in FPGA development `<scope>` could be:
- alg, algorithm
- fxp, fixed-point modeling
- rtl, register transmission level
- onb, onboard
- ...

Optional `!` could be used to mark significant changes.

`<description>` must contain succinct description of the change.
Use lowercase words, imperative tense, and no dot `.` at the end.

Optional `<body>` could include the motivation for the change and more details about the change.

Optional `<footer>` could contain other information about siginificant changes,
such as corresponding issues etc.


## History Recording

An README file helps to record development history and recall previous development,
which shall consist of **version summary** and overview of **current version**.


## Semantic Version Numbering

X.Y.Z

X is the **major** version, corresponding to solution changes.

Y is the **minor** version, corresponding to functionality changes.

Z is the **patch** version, corresponding to bug fixing and other codes changes.

Referenced from [Semantic Versioning](https://semver.org/).


# Project File Organizing


## For FPGA development

- \<pj\_name\>
    - docs
    - refs
    - data
    - algorithm
    - fixed\_point
    - fpga
        - src
        - [rm]
        - tb
            - \<module\_name\>
            - ...
        - sim
            - \<module\_name\>
                - [testcase]
                    - [cur\_case]
                    - [err\_case\_archive]
                        - [\<id\>]
                - [auto]
            - ...
        - \<eda\_project\>
        - ...
    - lib
        - \<language1\>
        - \<language2\>
    - ...

The directory identifiers between '\<' and '\>' should be replaced with specific names.
The '\<id\>' for error case is recommended to be an 8-digit number padded with 0.

The directories with '[' and ']' is optional.
'rm' directory is for reference model.

The 'testcase' directory might include scripts for testcase generation and testcase.
The 'cur\_case' is for the testcase currently under testing.

The 'auto' directory holds scripts for simulation automation.

