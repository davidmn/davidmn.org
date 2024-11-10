---
title: "Clojure REPL in VS Code"
date: 2021-05-14T21:10:52.00Z
draft: false
---

I'm tinkering around with Clojure at the moment and have found the tooling to be quite nice.

My setup is:

- VS Code running on my Windows 10 machine
- WSL 2 running Ubuntu 20.04
- Installed leiningen on Ubuntu
- Open VS Code and connect to the Ubuntu machine with the [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) plugin installed on Windows
- Install [Calva](https://marketplace.visualstudio.com/items?itemName=betterthantomorrow.calva) on the Ubuntu side[0]
- Open up your clojure project and then "jack in" to the repl with "Calva: Start A Project REPL and Connect" which you can find in the standard CTRL+SHIFT+P menu, select leiningen and you're away.

![](/content/images/2021/05/image.png)

The left pane shows your files while the right shows the REPL output.

Start running lines (forms) by selecting it with your cursor and hitting SHIFT+ENTER or by running "Calva: Evaluate Current Form" which will output in the REPL window on the right and show it next to the form in the text editor.

![](/content/images/2021/05/image-1.png)

Then running the entire file/namespace is done with "Calva: Load Current File and Dependencies":

![](/content/images/2021/05/image-2.png)

That's it for now.

[0] - I imagine running it all on your local machine also works
