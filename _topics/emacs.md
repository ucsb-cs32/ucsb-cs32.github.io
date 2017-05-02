---
topic: "emacs"
desc: "general purpose text editor with programmer-friendly features"
category_prefix: "emacs: "
---




# Quick Reference Guides 

* One page (basic) guide (PDF) <http://www.gnu.org/software/emacs/refcards/pdf/refcard.pdf>
* One page (cartoon) guide (PNG) <http://sachachua.com/blog/wp-content/uploads/2013/05/How-to-Learn-Emacs-v2-Large.png>


# A Really Short Intro to Emacs

If you want to use <b>emacs</b> to create and edit a new file (let's call it <b>hello.cpp</b>), then on a terminal, type:

```
$ emacs hello.cpp
```
Once emacs is open, you can start editing the file immediately. To move around, use <b>the arrow keys</b>.

<b>emacs</b> commands usually take the form <b>C-x C-c</b>. This means press and hold the <b>control</b> key while pressing the <b>x</b> key, then next press and hold the <b>control</b> key while pressing the <b>c</b> key. In contrast, the command <b>C-x 1</b> means press and hold the <b>control</b> and <b>x</b> keys, then release both and then press the <b>1</b> key.

Some useful commands are:
<ol><ul>
	<li><b>C-x C-c</b> closes emacs</li>
	<li><b>C-x C-s</b> saves the current file</li>
	<li><b>C-x 1</b> closes all other panes except the current one (you can use this close the help panel)</li>
	<li><b>C-g</b> at any point in a command will cancel the command</li>
	<li><b>C-k</b> "kills" or cuts the current line, and <b>C-y</b> "yanks" or pastes the line back at the cursor location</li>
</ul></ol>



# How To 

* Line-numbers on the side: `M-x linum-mode`
    * To make this stick, see [emacs: customization](/topics/emacs_customization/)

# What do `C-x`, `C-c`, and `M-x` mean? 

* `C-x` means "control X".  Hold down the control key and press the x.
* `C-c` means "control C".  Hold down the control key and press the c.
    * This pattern works for any sequence that starts with C-
* `M-x` means "meta X".  
    * There may be a "meta" key on your keyboard that works like a control key.  
    * If so, this works just like Control, except using a different key (hold down meta, and press x.)
    * But if not, use the escape key, but HERE'S THE THING: do NOT HOLD DOWN escape.  
    * Instead press escape, then RELEASE, then press x and release.
    * This is awkward the first few times you do it, then you get used to it.

# About `M-x`, the "named command" key 

There are lots of commands in emacs that are "named commands", and not bound to any key by default.  
To execute them, you bring up the emacs "command line", which you do by typing `M-x` (type `escape` once, then `x` once.).



