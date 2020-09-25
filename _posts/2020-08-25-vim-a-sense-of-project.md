---
layout    : post
title     : "Vim: A sense of project"
date      : 2020-08-25 12:00:00 -0500
categories: vim shell neovim
---

Vim (or [Neovim](https://neovim.io/)) is an excellent text editor, it is lightweight, ubiquitous and customizable in its core, but those are secondary traits, what makes Vim shine are its fast-writing capabilities with powerful keyboard-centered composable commands.

Despite its benefits, there is a list of things which Vim can’t do like:

- Advanced file management.
- GIT integration.
- Advanced code completion.
- Code linting.
- Pairing sessions.
- Global find and replace.

All of those are indispensable tools for everyday development. Are Vim users condemned to live without them? Not at all, and I’ll show you how you can create a better Vim set up so you can access the tools that you value the most and without losing the power of Vim.

**Disclaimer:** *This post is not a total-beginners guide to Vim. If you have previously configured Vim, then you are good to go.*

Vim’s built-in customizability allows the community to create plugins for it, and [VimAwesome](https://vimawesome.com/) is the Hub for almost all public plugins available, check it out, as its name suggests: it is awesome (you can navigate it with the Vim keys).

Adding plugins to Vim is straightforward:

1. Download it.
1. Add it on your config file (.vimrc or init.vim) by sourcing it.
1. Source again your config file or close/open Vim.
1. Enjoy!

Even though it is not hard to do it, it is tedious, so I recommend you to use a Plugin manager like [Plug](https://github.com/junegunn/vim-plug) or [Vundle](https://github.com/VundleVim/Vundle.vim). I recommend Plug because it is the one I have used.

## File management

### netrw
Vim comes out of the box with a file explorer named netrw, in order to use it, you can enter `:Exp` or `:Explorer` on normal mode. It will show a list of files in the current directory (you can navigate them with the usual keys), if you open a file and want to get back to the file explorer, you can use `:Rex` in normal mode.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-1.webm" | absolute_url }}" type="video/webm">
</video>

It seems that you can’t create new files or directories from it. That’s because it is such an obscure tool to use. Use `:help Explore` or press F1 on the explore screen to see the full list of commands.

For example, to create a new file in the current directory, use the `%` key; this will prompt you to name a new file.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-2.webm" | absolute_url }}" type="video/webm">
</video>

You can sort the explorer entries, open them on split or on another tab, hide dot-files, show/hide files with custom filters, create bookmarks, execute arbitrary commands to marked files, and a few other exciting commands.

### NERDTree
If you happen to love the way trees present your project’s files, you may be better with the [NERDTree](https://vimawesome.com/plugin/nerdtree-red) plugin, a powerful tool for file management.
NERDTree allows a lot of plugins to enhance its capabilities; I’ll show you a few of them later on.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-3.webm" | absolute_url }}" type="video/webm">
</video>

You can create a shortcut to fast-toggle the NERDTree, in your config file:

{% highlight vim %}
" By default, your leader is the \ key
nnoremap <leader>f :NERDTreeToggle<CR>
{% endhighlight %}

Also, you can see the plugin documentation pressing ? when NERDTree is open.

### FZF
And what about searching for files by their name or contents? Here comes [FZF](https://vimawesome.com/plugin/fzf), it allows you to use the FZF tool for fuzzy file searching, and on top of that, it allows you to use any other searcher like ‘git grep’, ripgrep or the silversearcher (I use the ripgrep tool).

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-4.webm" | absolute_url }}" type="video/webm">
</video>

You can create a shortcut the same way as on the well-known [ctrlp](https://vimawesome.com/plugin/ctrlp-vim-red) finder, for doing so, you need to map the FZF plugin to the ctrl-p shortcut (and also configure FZF to use ripgrep as its default command):

{% highlight vim %}
noremap <c-p> :FZF<CR>
" Fuzzy finder configuration to use the ripgrep tool
let $FZF_DEFAULT_COMMAND = 'rg --files --hidden'
{% endhighlight %}

You can use the Rg tool to search for file contents:

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-5.webm" | absolute_url }}" type="video/webm">
</video>

You can map that command to `ctrl-s`:
{% highlight vim %}
noremap <c-s> :Rg<CR>
{% endhighlight %}

## Project-wide find and replace
Project-wide find and replace might be the hardest thing to achieve in Vim without feeling that you did something that you shouldn’t have. There are a lot of ways of doing this. I’ll present to you the easiest one (though, not the best).

By default, Vim comes with `:grep` and `:vimgrep`, they both allow you to filter files by their content and save that list in the quickfist list. Once screened, you can make replacements only in those files by using the `:cfdo` command, for example, to find and replace the function named `read_file`:

1. First, you configure Vim’s hidden feature `:set hidden` to allow you to edit multiple files without errors for not having saved them.
1. This step is not needed, but makes the process faster by only processing the required files (select only ruby files with `read_file` in them) with `:grep -r read_file *.rb`
1. And finally replacing them with `:cfdo %s/read_file/read/gc | update`

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-6.webm" | absolute_url }}" type="video/webm">
</video>

This method uses the Unix’s grep tool and won’t omit files that you may no want to use like `node_modules` or ignored files by your source control system; I’ll leave a couple of interesting posts at the end of this one so you can read more about this find-and-replace stuff.

## Project workspace
You close Vim and call it a day, next morning, when you open the editor you probably have forgotten which files you were working on and how you arranged them. Well, you don’t have to go through that hassle anymore, Vim has sessions that allow you to save your workplace as you left them the last time.

In normal mode, enter `:mksession` to create a new session file in your working directory (by default called `Session.vim`), if you want to open Vim with that specific session, open Vim with the `-S` flag and you are good to go.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-7.webm" | absolute_url }}" type="video/webm">
</video>

The downside of using sessions is that you have to override them every time you have to change your workspace layout or open files. To overcome this issue, you can use [Obsession](https://vimawesome.com/plugin/obsession-vim) to manage all session-related stuff for you. Once installed and in normal mode, enter `:Obsession` to start tracking your session.

Remember always to start Vim with `vim -S` to use your `Session.vim` file.

## Code Completion
By default, Vim can complete based on dictionaries, opened buffers, and tags files; this can fall short compared to other text editors.

Also, this is a controversial subject for Vim users, and there is no unanimous verdict on what a completion tool should or shouldn’t do, I’ll list a couple of plugins for code completion:

- [COC](https://vimawesome.com/plugin/coc-nvim) has support for a lot of languages, function signature completion, and a handful of extensions.
- [youcompleteme](https://vimawesome.com/plugin/youcompleteme) has a lot of search engines for different languages.
- [Supertab](https://vimawesome.com/plugin/supertab) allows you to use the complete tool using the tab key.

## Code linters
There are a bunch of useful plugins for code linting, but the following two are my picks:

- [Ale](https://vimawesome.com/plugin/ale)
- [Neomake](https://vimawesome.com/plugin/neomake-impatience-and-laziness)

Whichever plugin you want to use will work (I use Neomake).

## Code navigation
If you want to go directly to a function declaration, Vim has built-in support for it. Vim takes advantage of the tags file, but you need [Ctags](https://github.com/universal-ctags/ctags) to generate that file. The downside of it is that you have to update that file every time you make a significant change on your codebase, to avoid that tedious process, use [Gutentags](https://vimawesome.com/plugin/vim-gutentags), which handles all the tags-related tasks.

To go to a code declaration, position the cursor on the specific method or class name and use `ctrl + ]`, if there’s a tags file with that definition, it will move you directly to its declaration.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-8.webm" | absolute_url }}" type="video/webm">
</video>

## Code snippets
I don’t use snippets, but if you do, there are a lot of plugins for it. The most known and used is the [Snipmate & Nltisnip Snippets](https://vimawesome.com/plugin/vim-snippets) plugin.

Also, for commenting code, you can use the [NERD Commenter](https://vimawesome.com/plugin/the-nerd-commenter) plugin.

## Pairing sessions
Vim by itself, doesn’t have remote pairing capabilities. Still, it can be achieved with [tmate](https://tmate.io/) for fast SSH session creation with read-only capabilities (uses your Tmux configuration if you already have it).

## GIT integration
Since you can execute arbitrary commands in Vim, you can communicate with GIT, but that is not that fun, there are a couple of plugins to enhance the GIT capabilities inside Vim:

- [NERDTree Git Plugin](https://vimawesome.com/plugin/nerdtree-git-plugin) shows visual elements to your NERDTree, so you know that something is going on with them
- [Vim Gutter](https://vimawesome.com/plugin/vim-gitgutter) uses the status column to highlight new, edited, and deleted lines in your open buffers, show differences, etc.
- [Fugitive](https://vimawesome.com/plugin/fugitive-vim) to call any GIT command inside VIm, with fantastic diff, log, and blame capabilities.

## Spelling and Dictionary
### Spelling
Vim has built-in capabilities for spell-checking, highlighting, and correction. To enable them in normal mode enter `:set spell spelllang=en_us`. Use `[s` and `]s` to navigate between spelling errors, use `z=` to show the correction list, and use `zg` to add a new entry to the dictionary.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-9.webm" | absolute_url }}" type="video/webm">
</video>

### Dictionary
Vim also has built-in Dictionary completion, to use it, your system should have a words file, in Linux by default there’s a dictionary file at `/usr/share/dict/words` (you can download them), 

in normal mode enter `:set dictionary+=/usr/share/dict/words` to append it to existing dictionaries.

<video class="post-video" controls>
  <source src="{{ "/assets/images/posts/vim-a-sense-of-project-10.webm" | absolute_url }}" type="video/webm">
</video>

Use `ctrl + x` and `ctrl + k` to open the completion modal, use the `ctrl + p` and `ctrl + n` shortcuts to navigate it.

### Grammar
You can integrate a grammar checker with the [LanguageTool](https://vimawesome.com/plugin/languagetool) plugin, it integrates with the LanguageTool application.

## Large file editing
Vim tends to choke when you open a large file due to all the line numeration, syntax highlighting, and all the plugins working on that specific file. To edit a particular file, you can disable the tools mentioned above and then edit the file, or use the [LargeFile](https://vimawesome.com/plugin/largefile) plugin to do it for you.

## Final thoughts
All of this seems like a lot, and you may ask, why should I even care to do all of this if Visual Studio Code, or “Insert my favorite code editor” does that and more right out of the box? 

Well, the thing here is that Vim allows you to tailor the text editor as you please. Without significant shortcomings, Vim can be faster than any other code editor you have ever used, ready to be used in almost any Unix system that you come across (and windows too).

For me, it is the experience, that rewarding feeling that you get when you happen to do a tedious thing with a couple of commands; tools make the work experience, I’m sure that woodworkers don’t like a dull saw nor painters like cheap brushes.

### Useful blog posts and resources:
- [My Vim config file](https://github.com/lacf95/dot-files/blob/master/nvim/init.vim)
- [10 Vim plugins for Writers](https://tomfern.com/posts/vim-for-writers)
- [Vim: you don’t need NERDTree or (maybe) netrw](https://shapeshed.com/vim-netrw/)
- [Project-wide find and replace](http://vimcasts.org/episodes/project-wide-find-and-replace/)
- [Supercharging Vim: Blazing fast search and global replace](https://www.mattlayman.com/blog/2019/supercharging-vim-blazing-fast-search/)
- [Visual Studio Code Vim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
- [Browsing programs with tags](https://vim.fandom.com/wiki/Browsing_programs_with_tags)

## Notes
This is an updated version of this post:
[Vim: A sense of project](http://tangosource.com/blog/vim-a-sense-of-project/)
