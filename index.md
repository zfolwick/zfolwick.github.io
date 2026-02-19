## **[home](../index.md)** | **[today I learned](./til/index.md)** | **[longer posts](./posts/index.md)**

<br>
<br>

## How to view this page in curl
The basic command is: `curl -s https://zfolwick.github.io/$1/ | xq -x '*/body'`

This sends the output of curl to `xq`.

We can wrap this in a function:

```
ws() {
  curl -s https://zfolwick.github.io/$1/ | xq
}
```

stick this in a script, source it, and then call `ws`.  You should get the raw html output.  Pipe the result into `xq -x '*/body/*'`

```
$ ws
zfolwick
      
      today I learned

posts

resume

    anchors.add();
```

That anchors.add() is annoying.  Let's remove all javascript using 
```
wstxt() {
  curl -s https://zfolwick.github.io/$1/ | xq -x '*/body/*[not(self::script)]'
}
```

then, we can type `wstxt` and get:
```
$ wstxt
zfolwick
      

      today I learned

posts

resume
```

This is beautiful, but how do I navigate? We can view the html page which gives all the links, :

```
ws til/ --> the today I learned page|
ws posts/  --> the list of posts
ws til/explicit-language

```
or we can pipe the results to `xq -x '*/body/*[not(self::script)]'` and get plain text.

## Why would you do this to yourself?

Because, like me, you're a terminal nerd and think the internet is too cluttered. The simple zen of a terminal excites you. 