---
description: Learn how to use editor plugins when setting up av CLI from Aviator.
---

# Use Editor Plugins

## Pull-Request Title and Description Editing

When editing the Pull-Request title and description, the Aviator CLI creates a temporary file with a file extension `.av.md`. Most of the text editors recognize this as a Markdown file, and the file is mostly highlighted accordingly. However, this file treats `%%` as a line comment, which is not a part of Markdown, and these parts are not highlighted properly.

By making an editor recognize `.av.md` as a special Markdown file, you can get a better text highlighting.

#### Vim

Install [<mark style="color:blue;">https://github.com/aviator-co/av-vim-plugin</mark>](https://github.com/aviator-co/av-vim-plugin) as a Vim plugin. This plugin detects `.av.md` as `av-markdown` syntax, and it does a proper syntax highlighting as well as setting a line comment prefix. Use a Vim plugin manager to install this. For example, if you use [<mark style="color:blue;">vim-plug</mark>](https://github.com/junegunn/vim-plug):

```
Plug 'aviator-co/av-vim-plugin'
```
