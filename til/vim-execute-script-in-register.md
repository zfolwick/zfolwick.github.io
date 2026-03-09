# Execute a bash script in the buffer

Sources: 
* https://peateasea.de/relative-copy-paste-in-vim/
* https://thoughtbot.com/blog/beyond-basic-modal-editing-using-vims-command-line-mode



Emacs has org-mode, which adds `#+begin_src` and `#+end_src` block to your code, allowing you to control+c twice, and the code will execute with some output.  This is amazing and incredibly useful for learning and documenting your learning.

Vim (and now neovim) has ex-mode.  ex mode is incredibly useful and powerful.

`:'<,'>` is a handy little vim tool which says "starting from the beginning of the visual mode to the end of visual mode".  This could easily be ":2,8" instead, which stands for "starting from line 2 and moving to line 8,".  This is the beginning of a sentence, where what follows is a verb.  Let's say in vim we wanted to copy line 5 to line 23; for that we would write in the command bar, ":5t23".  Below is a list of useful ex-mode example commands I use daily:

|  command  | description |
+-----------+------------|
| `:5t23` | transfer (copy/paste) the content of line 5 to line 23 |
| `:4,9t16 | transfer the content of lines 4 through 9 to line 16, pushing everything down further. |
| `:'<,'>d | deletes a selected block of text |
| `:'<,'>w !bash | execute the selected text and replace it with bash output. |
| `'<,'>copy'>+1|-1,$!bash` | copy the selected text and paste it with one line below your selected text, then run the text from one line above the cursor to the end of the file in a bash shell and replace the output |

That last one was a doozy.  Let's build it up.

* `'<` - the beginning of the visual selection
* `'>` - the end of the visual selection
* `'<,'>` - the beginning to the end of the visual selection
* `copy` - the equivalant of ex-mode's `t` (transfer) command.  It copies the stuff from the location on the left side to the location on the right side.
* `'>+1` - the line after the end of the visual selection
* `|` - in vim, this separates commands to be run right after each other (like && in long bash one-liners)
* `-1,$` - the line before the current line, all the way to the end of file
* !bash - run what's in memory in bash and replace the current output in the text body.
* `-1,$!bash` - run the previous line, all the way to the end in bash, and then replace those lines with the output from bash.

Let's see an example:

With two lines of input, the command becomes: '<,'>copy'>+1|-1,$!bash

```bash
echo "hi"
echo "a second line of script"

hi
a second line of script
```

But 3 lines of a script with the same thing gets us

```bash
echo "hi"
echo "a second line of script"
echo "a third line"

echo "hi"
a second line of script
a third line
```

Notice the first "echo hi" isn't executed?

if instead of copying _after_ the selection, we copy to _before_ the selection, it offers us some more versatility.  Try running `:'<,'>t-1|'<,'>!bash` by highlighting the next 3 bash lines, running this command, and pressing enter twice (to get through the prompt):

```bash
echo "hi"
echo "a second line of script"
echo "a third line"
hi
a second line of script
a third line
```
Success!

This can run against an arbitrarily large command block.  Just select the region to execute:

echo "+++++++++++++++++++++++++++"
echo "File report:"
echo "Environment: $(uname -a)"
echo "file type: $(file ./shell-in-vim)"
echo "stat output: $(stat ./shell-in-vim)"
echo "md5sum: $(md5sum ./shell-in-vim)"
echo "report completed"
echo "+++++++++++++++++++++++++++"
+++++++++++++++++++++++++++
File report:
Environment: Darwin zfolwick45194 25.2.0 Darwin Kernel Version 25.2.0: Tue Nov 18 21:09:40 PST 2025; root:xnu-12377.61.12~1/RELEASE_ARM64_T6000 arm64
file type: ./shell-in-vim: ASCII text
stat output: 16777230 265694319 -rw-r--r-- 1 zfolwick staff 0 269 "Mar  9 11:31:59 2026" "Mar  9 11:31:57 2026" "Mar  9 11:31:57 2026" "Mar  9 11:31:57 2026" 4096 8 0 ./shell-in-vim
md5sum: 311f0f99441e1df8e65f33a4e0b7fe57  ./shell-in-vim
report completed
+++++++++++++++++++++++++++

It doesn't appear to take inputs, which is a bit sad, but that's fine if you need to run a script to generate a report, or want to try to learn some bash.
