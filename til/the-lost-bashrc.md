# The curse of the lost .bashrc file

I really like to have my configurations customized to my development workflow environment. I had just created a few useful functions which were very helpful, and wanted to move those to a place where they could be generally useful.  My bashrc was symbolically linked from my git repo to the ~/.bashrc directory, and I dutifully _did not_ add a lot of things which were work related.  Somehow, I ended up _deleting_ my .bashrc file, causing a huge loss of productivity, as several required configurations were there for work.

My solution:

keep my .bashrc minimal and create another .workrc file, which only exists on my dev machine, and _source_ that file in my .bashrc.

So, from my .bashrc file, add this line:

```
[ -s "$HOME"/.workrc ] && source "$HOME"/.workrc
```

and then, in my other RC file, add all the proprietary stuff I want:

```
#! /usr/bin/env bash
echo "sourcing work-related configuration..."

# datadog e2e testing
export DD_API_KEY="my_api_key"
export DD_APPLICATION_KEY="my_app_key"

#npm token
export GHE_NPM_TOKEN="secret_npm_token"

# NVM stuff
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion

# AWS stuff
export AWS_API_KEY="secret_aws_key"
...

echo "done"
```

Now, all my changes that are proprietary stay within the work-related file, and general config-related stuff is kept separate.

I can't believe it took this long to apply separation of concerns to such a basic part of my life.