---
layout: post
title: Proofreading in Vim
date: 2021-04-21 16:00:00 
category: [Linux]
tags: [Linux, script, PDF, programming, LaTex]
---

I have spent a lot of time in Vim writing in LaTex and some Markdown. Functionalities like spell checking are quite important, especially I mostly write stuff in my non-native languages. This article covers, hopefully, every aspect of proofreading LaTex and Markdown in Vim.

## Vim spell checking

Vim itself has already a fully functioning spell checker, see `:h spell` for more details. Basically one can use the command `:set spell spelllang=en_us` to start spell check. To enable spell check, one can have `set spelllang=en_us` in `~/.vimrc` or in `~/.vim/ftplugin/FILETYPE.vim` to give the setting to a specific file type. Then Vim should highlight unknown words. In normal mode, one can use the following commands

- `z=` to see suggestions
- `]s` and `[s` to go the next/previous spelling error
- `:zg` to add a new word
- `:zw` to remove a word

## Languagetool

Vim's spell checking is nice, but insufficient (at least for me). [Languagetool](https://languagetool.org) is a free and open-source grammar checker with _multiple-language_ support. It can spot spelling errors and some simple grammar mistakes of _plain text_. For it to work well with LaTex and Markdown, additional tools are necessary.

## Textidote

[textidote](https://sylvainhalle.github.io/textidote/) is based `languagetool` and checks the mistakes in LaTex, including spelling, grammar, and LaTex styles.

Basic usage: 
```
java -jar /PATH/TO/textidote.jar --check en --dict ~/.vim/spell/en.utf-8.add --output html example.tex > report.html
```
where `--check en` specifies the language and `--dict` is used to link to additional user-defined dictionary (e.g. Vim dictionary in `~/.vim/spell`). After the command, a report as `report.html` is generated.

This approach works quite well, also with LaTex project with multiple files. But I am not satisfied with the cumbersome approach. 

## Integrate languagetool into Vim workflow

`vimtex` ([github](https://github.com/lervag/vimtex)) is a Vim plugin for LaTex files and it [integrates](https://github.com/lervag/vimtex/blob/master/doc/vimtex.txt#L4779) `languagetool` with `YaLafi` ([github](https://github.com/matze-dd/YaLafi)). For me, this approach is not the best. Because I often work with multiple `.tex` files and the quickfix box of `vimtex` doesn't perform well.

Eventually, I chose `ale`, a linter to integrate `languagetool`. Example configuration can be found in [here](https://github.com/matze-dd/YaLafi#plugin-ale). `ale` is also able to check grammar and so on in Markdown file. Just add `let g:ale_linters.markdown = 'languagetool'`.

In my case, I added new words into the vim dictionary. To perfect the experience with `languagetool`, one should link Vim dictionary to `languagetool`. One can add custom dictionary to languagetool in `path/to/langaugetool/org/languagetool/resource/en/hunspell/spelling_custom.txt` if you wish for language to generate corrections if some types a similar word ([see here](https://dev.languagetool.org/hunspell-support)). I renamed the original file, then made a symbolic link from vim dictionary to the `spelling_custom.txt`

## Grammarly

It is hard to deny that Grammarly is superior, especially when one often forgets to add (definite) articles. It seems that Grammarly browser plugin work with `overleaf`, but I try to avoid its plugins. Easiest to do is to copy the plain text into Grammarly and check the text.  I used `detex` command (should be on most Linux machines) to generate a relative clean plain text: 
```
detex -rsl -e align,equation,subequations,equation*,align*,figure FILE.tex
```
and paste the output into their website.

## Extra

There is another helpful program for writing in Vim [proselint](https://github.com/amperser/proselint/). It gives suggestions to improve your prose and it works well with `ale` in Vim.
