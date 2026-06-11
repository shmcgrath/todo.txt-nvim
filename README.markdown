### (Apr 2015) Important note: mappings were changed!

As it was suggested on issue [#28](https://github.com/freitass/todo.txt-vim/issues/28) (and as recommended by vim's documentation), all mappings were changed to use `<localleader>` instead of `<leader>`. If you don't have `maplocalleader` set on your environment then yours is probably `\`. For more information on that regard, please take a look at `:h <Localleader>`.

### (Jan 2016) Note: Overdue date highlight and Python Optional dependency

A new feature was added to highlight dates in overdue tasks as an Error (as suggested on issue [#44](https://github.com/freitass/todo.txt-vim/issues/44)). It depends on a Python library, however, and as such will only be able to work if your version of Vim was compiled with the `+python` option (as most common versions do).

If your Vim installation does **not** have Python support, this plugin **will work just fine** but this feature will be disabled.

### Quick install

    git clone git://github.com/freitass/todo.txt-vim.git
    cd todo.txt-vim
    cp -R * ~/.vim

Also make sure your .vimrc contains
    
    syntax on
    filetype plugin on

You can set the `<localleader>` in your .vimrc file with 
    
    let maplocalleader="<yourKey>"

This plugin gives syntax highlighting to [todo.txt](http://todotxt.com/) files. It also defines a few mappings, to help with editing these files:

Sorting tasks:  
`<localleader>s`   Sort the file  
`<localleader>s+`  Sort the file on +Projects  
`<localleader>s@`  Sort the file on @Contexts  
`<localleader>sd`  Sort the file on dates  
`<localleader>sdd`  Sort the file on due dates  

Edit priority:  
`<localleader>j`   Decrease the priority of the current line  
`<localleader>k`   Increase the priority of the current line  
`<localleader>a`   Add the priority (A) to the current line  
`<localleader>b`   Add the priority (B) to the current line  
`<localleader>c`   Add the priority (C) to the current line  

Date:  
`<localleader>d`   Set current task's creation date to the current date  
`date<tab>`        (Insert mode) Insert the current date  

Mark as done:  
`<localleader>x`   Mark current task as done  
`<localleader>X`   Mark all tasks as done  
`<localleader>D`   Move completed tasks to done.txt  

This plugin detects any text file with the name todo.txt or done.txt with an optional prefix that ends in a period (e.g. second.todo.txt, example.done.txt).

If you want the help installed, run ":helptags ~/.vim/doc" inside vim after having copied the files.
Then you will be able to get the commands help with: `:h todo.txt`.

## Old ftdetect for todo
``` vim
" File:        todo.txt.vim
" Description: Todo.txt filetype detection
" Author:      Leandro Freitas <freitass@gmail.com>
" License:     Vim license
" Website:     http://github.com/freitass/todo.txt-vim
" Version:     0.4

" Save context {{{1
let s:save_cpo = &cpo
set cpo&vim

" General options {{{1
" Some options lose their values when window changes. They will be set every
" time this script is invocated, which is whenever a file of this type is
" created or edited.
setlocal textwidth=0
setlocal wrapmargin=0

" Mappings {{{1
" Sort tasks {{{2
nnoremap <script> <silent> <buffer> <localleader>s :%sort<CR>
vnoremap <script> <silent> <buffer> <localleader>s :sort<CR>
nnoremap <script> <silent> <buffer> <localleader>s@ :%call todo#txt#sort_by_context()<CR>
vnoremap <script> <silent> <buffer> <localleader>s@ :call todo#txt#sort_by_context()<CR>
nnoremap <script> <silent> <buffer> <localleader>s+ :%call todo#txt#sort_by_project()<CR>
vnoremap <script> <silent> <buffer> <localleader>s+ :call todo#txt#sort_by_project()<CR>
nnoremap <script> <silent> <buffer> <localleader>sd :%call todo#txt#sort_by_date()<CR>
vnoremap <script> <silent> <buffer> <localleader>sd :call todo#txt#sort_by_date()<CR>
nnoremap <script> <silent> <buffer> <localleader>sdd :%call todo#txt#sort_by_due_date()<CR>
vnoremap <script> <silent> <buffer> <localleader>sdd :call todo#txt#sort_by_due_date()<CR>

" Change priority {{{2
nnoremap <script> <silent> <buffer> <localleader>j :call todo#txt#prioritize_increase()<CR>
vnoremap <script> <silent> <buffer> <localleader>j :call todo#txt#prioritize_increase()<CR>
nnoremap <script> <silent> <buffer> <localleader>k :call todo#txt#prioritize_decrease()<CR>
vnoremap <script> <silent> <buffer> <localleader>k :call todo#txt#prioritize_decrease()<CR>
nnoremap <script> <silent> <buffer> <localleader>a :call todo#txt#prioritize_add('A')<CR>
vnoremap <script> <silent> <buffer> <localleader>a :call todo#txt#prioritize_add('A')<CR>
nnoremap <script> <silent> <buffer> <localleader>b :call todo#txt#prioritize_add('B')<CR>
vnoremap <script> <silent> <buffer> <localleader>b :call todo#txt#prioritize_add('B')<CR>
nnoremap <script> <silent> <buffer> <localleader>c :call todo#txt#prioritize_add('C')<CR>
vnoremap <script> <silent> <buffer> <localleader>c :call todo#txt#prioritize_add('C')<CR>

" Insert date {{{2
nnoremap <script> <silent> <buffer> <localleader>d :call todo#txt#replace_date()<CR>
vnoremap <script> <silent> <buffer> <localleader>d :call todo#txt#replace_date()<CR>

" Mark done {{{2
nnoremap <script> <silent> <buffer> <localleader>x :call todo#txt#mark_as_done()<CR>
vnoremap <script> <silent> <buffer> <localleader>x :call todo#txt#mark_as_done()<CR>

" Mark all done {{{2
nnoremap <script> <silent> <buffer> <localleader>X :call todo#txt#mark_all_as_done()<CR>

" Remove completed {{{2
nnoremap <script> <silent> <buffer> <localleader>D :call todo#txt#remove_completed()<CR>

" Folding {{{1
" Options {{{2
setlocal foldmethod=expr
setlocal foldexpr=s:todo_fold_level(v:lnum)
setlocal foldtext=s:todo_fold_text()

" s:todo_fold_level(lnum) {{{2
function! s:todo_fold_level(lnum)
    " The match function returns the index of the matching pattern or -1 if
    " the pattern doesn't match. In this case, we always try to match a
    " completed task from the beginning of the line so that the matching
    " function will always return -1 if the pattern doesn't match or 0 if the
    " pattern matches. Incrementing by one the value returned by the matching
    " function we will return 1 for the completed tasks (they will be at the
    " first folding level) while for the other lines 0 will be returned,
    " indicating that they do not fold.
    return match(getline(a:lnum),'^[xX]\s.\+$') + 1
endfunction

" s:todo_fold_text() {{{2
function! s:todo_fold_text()
    " The text displayed at the fold is formatted as '+- N Completed tasks'
    " where N is the number of lines folded.
    return '+' . v:folddashes . ' '
                \ . (v:foldend - v:foldstart + 1)
                \ . ' Completed tasks '
endfunction

" Restore context {{{1
let &cpo = s:save_cpo
" Modeline {{{1
" vim: ts=8 sw=4 sts=4 et foldenable foldmethod=marker foldcolumn=1
```
