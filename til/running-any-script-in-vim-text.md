# Executing a script in vim

When executing a vim buffer window, I was able to show how to [run a bash script in vim](./vim-execute-script-in-register.md).  But there's no reason to stop at bash.

The discovered bash running script:
```bash
'<,'>t-1|'<,'>!bash
```
![bash](./images/bash-in-vim.gif)

can be swapped out with anything else.

```bash
'<,'>t-1|'<,'>!python3
```
![python](./images/python-in-vim.gif)

In fact, there's no reason it couldn't do :face_vomiting: javascript!

```javascript
'<,'>t-1|'<,'>!node
```

Next question: this definitely works with _interpreted_ languages.  Does this work with compiled languages as well?