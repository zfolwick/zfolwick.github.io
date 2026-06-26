## **[home](../index.md)** | **[today I learned](./index.md)** | **[longer posts](../posts/index.md)** | **[github](https://github.com/zfolwick)** | **[resume](../resume/index.md)** |

<br>
<br>

# Doom emacs outside of git-bash.exe

## Don't blindly run scripts

If you follow the internet's advice on how to install doom emacs on windows, you'll run across [several](https://gist.github.com/hunterlawson/acf4f65f30867d1642926ad91bc9839d) tutorials, which all ...

After you are told to run `~/.emacs.d/bin/doom install`, you're often told to run `~/.emacs.d/bin/doom sync`. Obviously, run `~/.emacs.d/bin/doom.ps1 sync` instead.

After that, you may start emacs only to discover either your emacs client doesn't look like other doom emacs configurations you've seen.  If you're on base Windows, your emacs client may be pulling...

This will get emacs starting and at least _looking_ like doom emacs users... sort of.  Ultimately, to get it working, I had to upgrade emacs to version 30, then re-install doom, then migrate stuff...

1. ensure you have git-bash installed.
2. ensure you have `fd` installed (choco install fd works in an administrator powershell)
3. ensure you have `rg` installed (choco install ripgrep works in an administrator powershell)

after those three steps... NEVER TOUCH POWERSHELL AGAIN. Only use git-bash.exe.


ONLY USE GIT-BASH.EXE.

And finally, never run scripts blindly from the internet.

Did I warn you?  Yes.  Yes I did.

Will that stop all of you?  No..... no it will not.

I can't keep you from killing yourself, but I _can_ recommend looking up [DistroTube's Intro to org agenda and org-mode](https://www.youtube.com/watch?v=Ea_-TaEGa7Y).
