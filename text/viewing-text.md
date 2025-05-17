# Viewing Text Files

This document deals specifically and _only_ with viewing text on a command line, without use of a gui. There's plenty of reasons to need to need to view text in a terminal, without access to a gui. Below I discuss all the different ways I view files.

We start by creating a file:

```bash
echo "hello world" > helloworld.txt
```

and we can view its contents without leaving terminal in several ways. Below we talk about viewing files- first without a text editor at all, and then with a text editor, and then finally searching for a particular pattern in a text file.

## Without a text editor

```bash
cat helloworld.txt  # <--outputs the whole file

grep ".*" helloworld.txt # <-- also outputs the whole file

sed '' helloworld.txt # <-- also outputs the whole file

echo ",p" | ed -s helloworld.txt # <-- also outputs the whole file

awk '{print}' helloworld.txt # <-- also outputs the whole file

head helloworld.txt  # <-- outputs the first 10 lines by default

tail helloworld.txt # <-- outputs the last 10 lines by default

less helloworld.txt # <-- outputs a scrollable screen of text

more helloworld.txt # <-- same as `cat`
```

These commands- `cat`, `grep`, `sed`, `awk`, `head`, `tail`- form the basics of all file viewing that doesn't involve a text editor. `ed` is an ancient text editor which spawned much of the syntax for `sed`, `vi/vim`, and could be interesting to learn. Each of these will work on files of unlimited size (read: yes it'll work on your 8GB log file). If you stop reading here, you'll be able to get by. Continuing on, we'll answer some text file related questions.

### viewing a specific lines:

The above list appears broken up into two sections: viewing a whole file, and viewing a portion of a file, with `head` and `tail` obviously best suited for that; but all except `cat` are explicitly designed for viewing certain portions of text.  

We can view _only_ specific line numbers with the below commands- just change the number "2" for your specific line number:

```bash
echo "2p" | ed helloworld.txt

sed -n '2p' helloworld.txt

awk 'NR == 2 {print}' helloworld.txt

head -n 2 # outputs up to the interesting line

tail -n 1 # outputs only the last line in the file
```

It's often recommended that `head` and `tail` are grouped together to get a single output, but I find this to be suboptimal for very large (several MB/GB) text files. `sed`, `ed`, and `awk` are significantly better alternatives with lower overhead.

`less` displays text one page at a time. For example, `less my-file-name` and then type `100g` will display the file starting from the 100th line at the top of the screen. When exiting `less`, it will clear the text from the screen and display your terminal.

`more` behaves the same way, except when you quit, it will leave the text on the screen. `more my-file-name` followed by `100g` will navigate to the 100th line in a file. `more` is also found by default on windows machines (search for `more.exe` in powershell or cmd).


## Text Editors

Other commands drop the user into a terminal user interface. The first such one to exist, and which informs many *nix traditions over the last 50 years is `ed`.

### ed
`ed` can be used either as intended, dropping into the editor as shown below, or in a one-liner in conjunction with the `pipe` (`|`) command (shown above):

```
$ ed helloworld.txt
26
2p
goodbye world
```
Here, "26" is the number of bytes in the message. `2p` is the command to print line 2 of the file, which is then printed on the line.

`ed` is designed to be used for typing machines, where displaying characters takes _real, actual time_.  You'd think this is ridiculous in the 21st century, but just try to edit a document over ssh with a shoddy, slow connection. It is an editor that's in _every_ linux and unix distro; there's no real equivalent in windows- probably due to the fact that it came around much later than when teletype was a standard interface. Becoming proficient with `ed` will make you better at `ex` (`vi`), and `sed`, and navigating around effectively using `less` and `more`. In fact, you can effectively create a script to edit a file from the command line in `ed`, and pass it in, and then edit the file, all without leaving the terminal, much in the same way you'd use `sed` and `awk`.

### ex

`ex` is essentially the bridge between `ed` and `vi`, below. In fact, entering visual mode in `ex` will actually load all of your `vim` modules, and you may enter `ex` mode in `vim`. Less used for viewing files, `ex` is useful for multiline scripts where `vim` only allows you to see the last command executed to modify a file.

### vi/vim

`ed` inspired `vi`- the first widely used terminal UI text editor. It was later improved to `vim`, and more "recently" re-written in lua to `neovim`. Neovim cleaned up some cruft, standardized some coding patterns, and included a more commonly used language (lua) to replace vimscript, which is far more niche. 

Commands in `ed` can still be used in `vim`, as well as several other tools. You'll almost always hear about `vim`, as it's effectively superceded `vi`, though `vi` is installed by default on *nix systems, and `vim`/`neovim` need to be installed by the user. They're easily installed using the package manager of your choice. Once installed, `vim my_file.txt` will clear your terminal and display all the text on the screen (the equivalent of `,p` in `ed`). Once a text document is open, you can type the command `:Tutor` to get a basic tutorial. To exit vim, type `:q`.

`vi`/`vim` is technically more of a text editing language. You can use `ex` mode in `vi` to perform the same viewing or editing commands as `ed`.

### emacs
`emacs` is an _extremely_ powerful program that has a great text editor. It's heavier weight than `vim`, and uses different commands, and is just as extensible as `vim`.  While most people will use emacs in a GUI, it still has a terminal mode which can be useful, but requires a different set of configurations. In my mind, its chief benefit is its extremely powerful basic navigation techniques which, if learned, can enable you to move _blazingly_ fast in the terminal.  Additionally- like `vim`, it's infinitely customizable, thanks in part to its integration with lisp. I've also discovered that some apps like chrome and slack use limited emacs keybindings to allow users to navigate around rapidly in the URL address bar. To view a document, `emacs my_file.txt` is all you need. Far more than a text editor, some consider emacs to be an entire OS in itself, and use it entirely for their computing experience as you may use it as an email client, chat client, file manager, IDE... it's endless. Don't look for it by default though.

### nano

while emacs and vim have a notoriously steep learning curve, and `ed` doesn't so much have a learning "curve" as it has a learning _wall_, `nano`/`pico` is a terminal text editor that is both lightweight, reasonably intuitive to use, but nowhere _near_ as powerful as `vim` and `emacs`. In short... it's a text editor. it does what it says on the tin. You can edit documents without learning a bunch of extras, and it's often available on every machine by default.

# viewing patterns in text
`ed`, `ex`, `vi`, `vim`, `grep`, `sed`, `less`, and `more` all use the same language when looking for text via some search tearm.  When looking for text that matches a pattern, `grep` is the undisputed go-to command when you don't want to open a file. While it is convenient, and cross-platform, working the same in mac as it does in linux, all of its functionality can be gracefully duplicated by knowing `awk`, and `sed` which also allow for text editing. Of the two of these, I find `awk` to be the most portable (awk works on windows easily); although that doesn't diminish the value of `less`, and `grep` as read-only tools.

the following produce the same output:
```bash
$ sed -n '/hello/p' helloworld.txt 
$ grep hello helloworld.txt
$ awk '/hello/{print}' helloworld.txt
$ ed -s helloworld.txt <<< "g/hello/p"
```
Notice in the final command `g/regex/p` is used.  You will recognize the older abbreviation for "regex" as "re". This is _directly_ the inspiration for the naming of the tool `grep`(aka, `g/re/p`), which finds _globally_ within a text file, a _regex_, and then _prints_ it to the console.

There's a _deep_ well of knowledge, and learning to view files is probably the second thing one should learn after navigating between files and listing out files on the file system. Next we will investigate editing files using common tools.