---
layout: post
title: A Quick Vim Tips -- Using Markdown
---

[Vim](http://www.vim.org) is my text editor of choice. It doesn't have all of the niceties of [TextMate](http://macromates.com), so you often have to assemble them yourself through user scripts and configuration. One of those areas that Vim needed a little assistance in was its support of [Markdown](http://daringfireball.net/projects/markdown/). The following are some quick tips for improving Vim's handling of Markdown.

## Get Better Syntax Support

Ben Williams has written a fairly good syntax definition for Markdown. It's unfortunately not on Vim's script index, so you'll just have to [grab it from his blog](http://plasticboy.com/markdown-vim-mode/).

## Preview Support

Chances are your package management system has [Discount](http://www.pell.portland.or.us/~orc/Code/discount/) available. Install it. Once you do so, you'll have a command line tool called "markdown" at your disposal. Once you do so, open up your vimrc and add the following to it:

```vim
" Markdown preview
imap &lt;leader&gt;p &lt;ESC&gt;:w!&lt;CR&gt;:!markdown % &lt; %.html &amp;&amp; open %.html&lt;CR&gt;&lt;CR>a
map  &lt;leader&gt;p &lt;ESC&gt;:w!&lt;CR&gt;:!markdown % &lt; %.html &amp;&amp; open %.html&lt;CR&gt;&lt;CR>a
```

This will generate a preview of your Markdown file that you are currently viewing. If you're really clever, you'll also add "\*.mkd.html" to your gitignore file so that you don't have to worry about the generated preview.

## Soft Wrap Your Document

Head on over to [Vim Casts](http://vimcasts.org/episodes/soft-wrapping-text/) to learn about how to use soft word wrapping in your documents. I find soft wrapped text much more natural looking when writing documents.
