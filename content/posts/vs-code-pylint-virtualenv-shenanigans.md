---
title: "VS Code, pylint, virtualenv, pytest shenanigans!"
date: 2018-05-25T18:43:41.00Z
draft: false
---

Imagine you're writing some code. Maybe you're putting your recipes in git and doing CI/CD on them. Imagine you've got tests written in python using pytest. You're not a complete scrub and you're using a virtualenv. You've got pylint to lint your code. Your tests run. VS Code is highlighting `import pytest` and saying that pylint cant find the module. WAT?
 
You'll spend a while Googling but you wont find anything. Then you thing "hey, what interpreter am I using?". You click the interpreter in the bottom left of VS Code and see a list:
 
- Python 3.6.5
- Python 3.6.5 (virtualenv)

You notice you have the first one selected even though the intergrated terminal is in the virtualenv. You select the second item in the list.
 
Pylint is happy now. You are happy now.

