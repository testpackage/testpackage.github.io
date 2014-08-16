---
title: Test prioritisation
categories: feature
icon_class: icon-target
position: 1
---
Runs recently failed tests first, on the basis that these are most likely to identify persistent problems. This can be particularly powerful when used with fail-fast mode - checking whether you've fixed a failing test no longer involves waiting for dozens of other stable tests to be run first.