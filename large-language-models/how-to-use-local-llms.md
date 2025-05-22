# The heuristic grep

Up until OpenAI released chatGPT to the world, and we discovered what we could do with deep learning and vector databases, if someone wanted to understand a piece of code, or a document, they needed to be able to read the document. All tools performing summaries and analysis were simply extracting text and performing predefined analysis encoded meticulously by the developer. While that's certainly useful, `ollama` offers an offline method for removing _some_ of the tediousness from a basic search of a document.

In a [previous article](./leveraging-local-llms.md), I discussed appending a question to the end of a file, and then piping that input into ollama. We'd previously wrapped that up in a bash function called `ask-pkg` and placed that in our `.bash_aliases` file. Now, we'll extend this to handle all files.

## Extending ask-pkg
First, recall our original package.json file:

```json
{
  "scripts":{
    "build-for-windows":"./scripts/build.sh windows",
    "build-for-mac":"./scripts/build.sh windows",
    "build-for-linux":"./scripts/build.sh windows",
    "test":"jest-commands-for-api-tests",
    "unit-test":"jest-commands-for-unit-tests",
    "build-test":"shell-commands-to-build-for-test-environment",
    "build-stage":"shell-commands-to-build-for-stage-environment",
    "build-prod":"shell-commands-to-build-for-prod-environment",
    "test-local":"shell-commands-to-build-for-local-environment",
    "test:e2e":"playwright test --config=my/config/file.js",
    "test:a11y":"playwright test --config=my/a11y/config/file.js"
  }
}
```

Then recall the original function:
```bash
function ask() {
  # asks a question of files
  sed "\$ s#}\$#}\n${1}#" <(jq '.scripts' package.json) | ollama run llama3.2:latest
}
```

We will pull out the file input, turning `ask-pkg` into `ask`:

```bash
function ask() {
  # asks a question of files
  sed "\$ s#}\$#}\n${1}#" <(eval "${2}") | ollama run llama3.2:latest
}
```

This can be used by passing in the question, then the command used to pass in the the package.json file:

```
$ ask "what are the test commands in this file" "jq '.scripts' package.json"
```

But this means that the user still needs to parse the file, requiring knowledge of `jq`. For small files, we can instead use `cat`, but this isn't much of an improvement, and beyond the maximum number of tokens for our llm, our AI will start hallucinating:
```
$ ask "what are the test commands in this file" "cat test.json"
The test commands in this file are:

- `test`: This command runs Jest commands for API tests.
- `unit-test`: This command also runs Jest commands, specifically for unit tests. The difference is that it's explicitly mentioned as "unit-tests" 
instead of just "tests".
- `test:e2e`: This command runs end-to-end tests using Playwright with a configuration file specified by `my/config/file.js`.
- `test:a11y`: This command runs accessibility tests (a11y) using Playwright with a separate configuration file specified by `my/a11y/config/file.js`.
```

At this point you might decide to take a detour into prompt engineering:

```
$ ask "what are the test commands in this file. Use the minimum number of words possible" "cat test.json"
"test-e2e", "test:a11y"
```
This is clearly incorrect. Let's refine our question:
```
$ ask "what are the test commands in this file. Use the minimum number of words possible without leaving out any test commands" "cat test.json"
"test", "unit-test", "test-local", "test:e2e", "test:a11y"
```
Maybe _we_ can use fewer words?
```
$ ask "what are the test commands in this file. Don't leave out any test commands" "cat test.json"
The test commands in this file are:

1. `test`
2. `unit-test`
3. `test-local`
4. `test:e2e`
5. `test:a11y`

These commands are used for running various types of tests, including:

- `test`: Jest commands for API tests
- `unit-test`: Jest commands for unit tests
- `test-local`: Shell commands to build and run in the local environment
- `test:e2e`: Playwright test with a custom configuration file for end-to-end tests
- `test:a11y`: Playwright test with a custom A11y configuration file
```
You can see that our second prompt produced the most terse results, and our third query produced decent results with context, while our first results were just plain _wrong_.

Let's make a note that we need to do some prompt engineering, and return to the original problem of not wanting to push a whole file into our llm.

I want to simplify my interface to: `ask "QUESTION" FILE_NAME`, so the above question would simply be:
```
$ ask "what are the test commands in this file. Don't leave out any test commands" test.json
```
making the interface very similar to `grep`. But that is easier said than done. Without knowing _how_ to parse the text file, our function is left to interpret that from the _QUESTION_, leading us to discover just how much _work_ we as developers do. Additionally, using AI for getting results doesn't seem like it'll pan out very well:

```bash
  if [[ $(file "${2}" ~= "JSON file") ]]; then 
    local question="answer with only a yes or no. does the above json contain a field with a keyword from the prompt?"

    local hasField=$(sed "\$ s#}\$#}\n${question}#" <(cat "${2}") | ollama run llama3.2:latest)
    if [[ $hasField == "Yes." ]]; then
      local keyword_query="what is the name of the keyword. Only give the exact name inside the file."
      local keyword=$(sed "\$ s#}\$#}\n${keyword_query}#" <(cat "${2}") | ollama run llama3.2:latest)
      echo the keyword is:$keyword
    fi

    return
  fi
```
For the first question, we can reliably get a "Yes." or a "No." result from llama3.2:latest. The second question though, when asked "what scripts can I run?", the results were less than perfect:

```
$ ask "what scripts can I run?" test.json 
the keyword is: Script
```
No matter how I massage this, I cannot get `llama3.2:latest` to match correctly.

