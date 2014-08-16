---
title: Test subsets based on coverage
categories: feature
icon_class: icon-flash-outline
---
TestPackage can use JaCoCo to work out which tests achieve the most coverage, and to select which tests to run to 
achieve a desired coverage in the quickest time. 50% coverage good enough for deployment smoke tests or a first pass of 
functional tests? Specify `--optimize-coverage=50%` and let the tool figure out which tests to run. Or want to the best 
tests that fit into five minutes? `--optimize-time=5m` will do this for you.