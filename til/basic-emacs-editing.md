## **[home](../index.md)** | **[today I learned](./index.md)** | **[longer posts](../posts/index.md)** | **[github](https://github.com/zfolwick)** | **[resume](../resume/index.md)** |

<br>
<br>

# Emacs: Day 0

I used to use emacs back when I was contracting at Microsoft. I'd ask the old Linux grey-beards there whether I should use vim or emacs, and- _resoundingly_, every single person said "you should re...

So, here I am again, nearly 10 years later, setting up emacs from scratch.  I'll avoid doom emacs for now simply due to the fact it doesn't seem to work on windows or WSL easily.  That's fine- we s...

## Navigating
RTFM.  Everywhere tells you how to go up a line, down a line, forward on a line, backward on a line, and skip words, delete words, delete a line, paste a line.  I'm not recreating that particular w...

## A warning

Finally, give up on the idea that you'll set up doom emacs or spacemacs or anything else on windows.  It's too difficult at the beginning while you're learning everything else.  Just be content wi...

## Day 1: Setting up shop

### Create a file: the emacs config file

The first thing you need is a config file.  I've titled mine _init.el_, and it lives in a directory called _.emacs.d_, in my home directory. If you want to make this in emacs, there's two ways:  C...

### Saving a file: save the config file

C-x C-s will save the file.  If you used only emacs to create the file, up to this point, nothing exists yet on the file system (much like a regular text editor).  Saving will create a new object ...

### The first changes to a file: simplifying the view

The emacs initial view out of the box has a toolbar, a menu bar, and a scroll bar that I don't like.  I like to remove them.

```
(menu-bar-mode -1)
(tool-bar-mode -1)
(scroll-bar-mode -1)
```

Entering these in, and then saving them, we can restart emacs and have a simpler view.

### Copy text from emacs to outside of emacs

Select the region using C-SPC (Control+SPACEBAR), go to the end of the region you'd like to copy, and then press M-w.

M-w == copy

### Copy text from elsewhere into emacs

Copy the target text the way you normally would, use C-y to "yank" the lines into the buffer window.  That's how you paste inside emacs.

### Undo text

C-/ for code changes or C-_ (my preferred method).

### Themes and Dark Mode

M-x load<TAB> should bring up a list of functions starting with the text "load".  One of those should be "load-theme". When prompted to enter the theme, press <TAB> again, and a list of themes sho...
```
(load-theme 'wheatgrass t)
```
Your config file should now look like:

```
(menu-bar-mode -1)
(tool-bar-mode -1)
(scroll-bar-mode -1)

(load-theme 'wheatgrass t)
```

Now restart emacs. It should be a pleasant dark mode.  There's tons of different themes to choose from- the built-in themes aren't as extensive as vim or neovim, but they're acceptable and install...

### Kill the current buffer

Press C-x k (Control+x, then k).

### Org mode
The reason I wanted to try emacs in the first place was because I'd heard so much about org-mode. Org-mode is installed with emacs by default, but check with https://orgmode.org/ to stay current o...

Placing `(setq org-hide-emphasis-markers t)` allows stuff to generally be rendered in the the screen.

Create a file called test_file.org.  We'll start with bullet pointed items and decent text collapsing.

#### Bullet points

```
* Welcome to org-mode
** sub heading
some text
some other text
*** another heading.
```

We can render it as html via C-c, C-e, then reading the output window, we see the option to export to HTML via "h", then "o".  Then your browser will open with the bullet points displayed.

We can also collapse these because in org files, '*' indicates levels. We can fold our text up by placing the cursor and the end of the line on "welcome to org-mode" and pressing <TAB>. We can slo...

For the sections below, we'll create new bullet points at the top level.  Experiment with <TAB> and <SHIFT>+<TAB> and the effects from that.

#### Tables
Tables are similar to markdown, though the header separator is slightly different.  In your test_file.org, create a bullet point for a table:

```
* A table example

| name  | SSN | description    |
|-------+-----+----------------|
| jimmy | 123 | slippin' jimmy |
| jim   | 345 | jungle         |

```

Note the "+" sign as the biggest difference between tables in markdown. Another difference comes by pressing <TAB> while your cursor is inside the table.  This makes it easier to navigate through...

#### Code running

I'm by no means an expert at this, but it appears emacs has jupyter notebook like functionality, but for more than just python (and yes, python appears to be included).  Check out the tutorial on...

By adding the following lines to my test_file.org, I was able to run commands in different languages.  I start with a new bullet point:

```
* Code examples:

```

and I can add the following code:

```
#+HEADER: :var data1=1
#+BEGIN_SRC emacs-lisp :var data2=2
   (message "data1:%S, data2:%S" data1 data2)
#+END_SRC
````

Placing the cursor inside the code block defined above, and typing C-c, C-c, I get a prompt: `Evaluate emacs-lisp code block on your system? (yes or no)`.  Type 'yes'. You should get the result:

```
#+RESULTS:
: data1:1, data2:2
```

To run something outside of elisp, place the following in your init.el file:

```
(org-babel-do-load-languages
 'org-babel-load-languages
 '((python . t)
   ;; Add other languages you use here, e.g.,
   ;; (shell . t)
   ;; (R . t)
   ))

```

Then restart emacs, then re-open test_file.org.  Navigate to the bottom of the file and put some python in. The example taken from the orgmode.org site:

```
#+NAME: less-cols
| a |
|---|
| b |
| c |

#+BEGIN_SRC python :var tab=less-cols :colnames nil
return [[val + '*' for val in row] for row in tab]
#+END_SRC
```
Then you can place your cursor here and begin evaluating the text via C-c, C-c.  You should get these results below:

```
#+RESULTS:
| a  |
|----|
| b* |
| c* |

```

Some other day, I'll try it out on several different types of code, but for now, I've duplicated the functionality of jupyter notebooks.

### eshell: a sane shell for windows users!

Powershell is... something. But it's nothing like bash, and just installing emacs gives you a shell mode that seems to have all the coreutils installed already.  Start a new buffer in emacs with ...


### Installing packages: the MELPA library

MELPA (Milkypostman's Emacs Lisp Package Archive) is the first package manager for emacs you'll encounter, though it's not the only one. Navigate to MELPA's [getting started](https://melpa.org/...

```
(require 'package)
(add-to-list 'package-archives
             '("melpa-stable" . "https://stable.melpa.org/packages/") t)
```
Now restarting emacs and typing "M-x packages<TAB>" gives the list of _package_ commands. A buffer will open with the available packages from MELPA.  It's a lot.

I originally started this thinking I'd need to install a package, but- Today I learned that org-mode comes installed by default with emacs!
