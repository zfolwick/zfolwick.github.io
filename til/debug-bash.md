## **[home](../index.md)** | **[today I learned](./index.md)** | **[longer posts](../posts/index.md)** | **[github](https://github.com/zfolwick)** | **[resume](../resume/index.md)** |

# Debugging bash scripts

using `set -x` at the top of your scripts is nice for debugging purposes, but creates some weird behavior for me when I run it with my .bash_aliases file.  What I just learned is a forehead-smacking "I should have thought of that!" moment from Dave on youtube at "You Suck At Programming".

Consider the below bash hello world example:

```
#!/usr/bin/env bash
#extension: sh

debug() {
    if [[ -n "$DEBUG" ]]; then
        echo '[DEBUG]' "$@" >&2
    fi
}

echo "Hello, World!"
debug "This is a debug message."

```

It is calle on the command line via `DEBUG=1 ./helloworld.sh`.  THis is the cleanest I've ever seen it implemented!  So fast I didn't even notice it was implemented.  Instead of odd behavior, I have tight controls.