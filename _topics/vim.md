---
topic: "vim"
desc: "a widely used text editor among Unix users"
---

# A really short intro to vim


To run vim from the terminal, just type in "vim" (or sometimes "vi" works).

```
$ vim hello.cpp
```
Once vim is open, you can start editing the file immediately. 

<h2>Basic use</h2>
\<Esc\> is the escape key or use \<ctrl\>[  sometimes written as C-[<br/>

vimtutor    : starts vim editing a copy of a tutorial file -- very good.<br/>
i           : insert mode. Next keys typed are inserted into the file.<br/>
\<Esc\>     : Escape from insert mode so you can navigate and use edit commands (stop selecting)<br/>
h j k l     : move cursor ( h: ←  j: ↓  k: ↑  l: → )<br/>
A           : Append at end of line<br/>
o           : Insert at new line below<br/>
u           : undo last command, again and again<br/>
x           : delete character under cursor<br/>
dw          : delete everything right from the cursor to the start of next word (and put it into the default register)<br/>
dd          : delete line (and put it into the default register)<br/>
p           : paste the default register<br/>
<br/>
/myname     : search forward for myname<br/>
<br/>
:wq         : write and quit<br/>
:x          : write and quit<br/>
:w filename : write a copy of the file you are editing as filename<br/>
:q!         : quit without saving even if changes were made!<br/>
:help       : display help<br/>
\<Tab\>       : use tab completion to scroll through commands that start with what you typed<br/>
<br/>
COPY PASTE  (for CUTting lines use dd as described above)<br/>
v           : visual mode -- use to select text with cursor movement or mouse<br/>
y           : use to yank (copy) what was selected<br/>
\<Esc\>       : esc gets you back to the main mode<br/>
<br/>
^ w e $     : bigger movements: beginning of line, word, end of word, end of line<br/>
<br/>
Modes:<br/>
 normal, insert and visual, there are others too<br/>
 \<Esc\>    takes you back to normal<br/>
<br/>
Enter a number before a command to repeat it, examples:<br/>
   10w      : skip forward 10 words<br/>
   10dd     : delete 10 lines<br/>
<br/>
Commands are case sensitive:<br/>
   c        : starts a change command<br/>
   C        : change to end of line (same as c$)<br/>
   ce       : change to end of word (a complete change command)<br/>
<br/>
<h2>Other useful hints</h2>
www.vim.org   : Visit frequently<br/>
comp.editors  : Vim dominated newsgroup<br/>
* # g* g#     : find word under cursor (forwards/backwards)<br/>
%             : match brackets {}[]()<br/>
\<C-N\> \<C-P\>   : word completion in insert mode<br/>
\<C-X\>\<C-L\>    : Line complete SUPER USEFUL<br/>
\<C-R\>\<C-W\>   : Pull cword onto search/command line<br/>
:set ignorecase : you nearly always want this<br/>
:set smartcase  : case-sensitive if search contains an uppercase character<br/>
:syntax on    : colour syntax in Perl,HTML,PHP etc<br/>
:h slash\<C-D\> : type control-D and get a list all help topics containing slash<br/>


# What is vim?

Historically, there are two widely used text editors among Unix users: `vi` and `emacs`.  

* `vim` is the most recent version of vi
* `vim` stands for <tt><b>vi</b> i<b>m</b>proved</tt>
* `vim` and `emacs` tend to have long arguments about which is better.  
    * These arguments are pointless and tedious.   
    * They are both good; they just have different design tradeoffs.
    
If you want to learn about emacs instead of vim, visit the [Emacs Page](/topics/emacs/) on this website.
Since this page is about vim, we won't mention emacs further.   

# Quick Reference Guides 

* Vim wiki http://vim.wikia.com/wiki/Category:Getting_started
* Vim tutorial http://vim.wikia.com/wiki/Tutorial

# How To

* Line-numbers on the side: `:set nu`  
    * To make this persist between invocations of vim, see [Vim: Customization](/topics/vim_customization/)

* What do  `:w`, `:wq`, and `:wq!` mean?

* `:w` means "write".  Hit "Escape" and type in the character sequence to write your edits.
* `:wq` means "write and quit".  Hit "Escape" and type in the character sequence to write and quit from the Vim environment.
* `:q!` means "quit without saving".  

And a bonus:

* `:u` means "undo".



# Finding and Replacing

* To go to a specific line number: `:[LINE_NUMBER]`
    * For instance, if I was trying to go to line 181 in a file, I would enter `:181`

* To search for a character string: `/[STRING]`
    * Note that you may need to use the escape character `\` before any special characters 

* To search/replace (e.g. change `old` to `new`):
    * To replace the first encountered instance of a string: `:%s/old/new/`
    * To replace all instances of a string: `:%s/old/new/g`
