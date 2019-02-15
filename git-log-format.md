---
title:  GIT日志格式
date: 2019-02-12 21:17:00
tags: [GIT, 日志]
categories:  GIT
---
### GIT日志格式

##### 格式报文

```bash
<type>(<scope>): <subject>

<body>

<footer>
```
##### subject的值

* First line cannot be longer than 70 characters, second line is always blank and other lines should be wrapped at 80 characters. The type and scope should always be lowercase as shown below

##### type的值

*   **feat** (new feature for the user, not a new feature for build script)
*   **fix** (bug fix for the user, not a fix to a build script)
*   **docs** (changes to the documentation)
*   **style** (formatting, missing semi colons, etc; no production code change)
*   **refactor** (refactoring production code, eg. renaming a variable)
*   **test** (adding missing tests, refactoring tests; no production code change)
*   **chore** (updating grunt tasks etc; no production code change)

##### scope的值

*   init
*   runner
*   watcher
*   config
*   web-server
*   proxy
*   etc.

> The `<scope>` can be empty (eg. if the change is a global or difficult to assign to a single component), in which case the parentheses are omitted. In smaller projects such as Karma plugins, the `<scope>` is empty.

##### body的值

*   uses the imperative, present tense: “change” not “changed” nor “changes”
*   includes motivation for the change and contrasts with previous behavior

##### footer的值

Closed issues should be listed on a separate line in the footer prefixed with "Closes" keyword like this:

```bash
Closes #234
```
[参考1](http://karma-runner.github.io/0.13/dev/git-commit-msg.html)