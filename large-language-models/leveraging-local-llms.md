# Using Local Large Language Models (L3M?)

Large language models are slightly more "intelligent" than text messages constructed using the [predictive text game](https://www.rockpapershotgun.com/have-you-played-the-predictive-text-game), and as statistical models, with randomness built in, they must be used with care.

They also are prone to "hallucinations"- an artifact of... essentially feeding it more information than it can handle before it forgets the beginning of the text block fed to it. Kind of like that friend who uses extremely long sentences before getting to the point, and now you've forgotten the beginning of the question. Interestingly, this doesn't stop the LLM from confidently choosing the most likely stuff based on what it _does_ "remember".

Despite this shortcoming, however, LLMs seem to be here to stay, and _can_ provide some limited benefits as a summarizer of code. One thing we can do is feed `ollama` a file, and then at the end, ask a question.

In a new nodejs project that we're onboarded on, we can get an easy sense of what scripts are used by running `jq` on the _package.json_:
```bash
$ jq '.scripts' package.json
{
  "build-for-windows": "./scripts/build.sh windows",
  "build-for-mac": "./scripts/build.sh windows",
  "build-for-linux": "./scripts/build.sh windows",
  "test": "jest-commands-for-api-tests",
  "unit-test": "jest-commands-for-unit-tests",
  "build-test": "shell-commands-to-build-for-test-environment",
  "build-stage": "shell-commands-to-build-for-stage-environment",
  "build-prod": "shell-commands-to-build-for-prod-environment",
  "test-local": "shell-commands-to-build-for-local-environment",
  "test:e2e": "playwright test --config=my/config/file.js",
  "test:a11y": "playwright test --config=my/a11y/config/file.js"
}
```
But you can see how, very quickly, this can get untenable in a large project that's not regularly refactored. We can append our question to the end of this file easily using `sed`:

```bash
$ sed '$ s#}$#}\nwhat kinds of tests are available in the above npm scripts?#' <(jq '.scripts' package.json)
{
  "build-for-windows": "./scripts/build.sh windows",
  "build-for-mac": "./scripts/build.sh windows",
  "build-for-linux": "./scripts/build.sh windows",
  "test": "jest-commands-for-api-tests",
  "unit-test": "jest-commands-for-unit-tests",
  "build-test": "shell-commands-to-build-for-test-environment",
  "build-stage": "shell-commands-to-build-for-stage-environment",
  "build-prod": "shell-commands-to-build-for-prod-environment",
  "test-local": "shell-commands-to-build-for-local-environment",
  "test:e2e": "playwright test --config=my/config/file.js",
  "test:a11y": "playwright test --config=my/a11y/config/file.js"
}
what kinds of tests are available in the above npm scripts?
```
in the above code, we can break it down thus:

`sed`: stream editor which we will use to edit the last line of a file.  
`$`: in the language of `sed`, `$` represents the last line of the file; so we instruct sed to move to the last line of the file.  
`s#}$#}\nSOME TEXT#`: in the language of `sed`, `s`ubstitute `}` on the final line with a newline character after `}`, followed by `SOME TEXT`.  
The `#` characters are field separators for `sed`; normally shown using `/`.  
`<(bash_script)`: This redirects output from a `bash_script` (in our case `jq .scripts package.json`) into the command that it points to.
`jq .scripts package.json`: parses package.json and only presents the stuff in the "scripts" section.

And finally we can pipe the output here to ollama:
```bash
$ sed '$ s#}$#}\nwhat kinds of tests are available in the above npm scripts?#' <(jq '.scripts' test.json) | ollama run llama3.2:latest
Based on the provided npm script configurations, the following types of tests are available:

1. **Unit Tests**: Configured with `"unit-test": "jest-commands-for-unit-tests"` and using Jest as the testing framework.
2. **API Tests**: Configured with `"test": "jest-commands-for-api-tests"`, also using Jest as the testing framework.
3. **End-to-End (E2E) Tests**: Configured with `"test:e2e": "playwright test --config=my/config/file.js"` and using Playwright for end-to-end testing.
4. **Accessibility (A11y) Tests**: Configured with `"test:a11y": "playwright test --config=my/a11y/config/file.js"`, also using Playwright for A11y 
tests.

These tests cover unit testing, API testing, E2E testing, and A11y testing, providing a comprehensive set of tests to ensure the application's quality 
and functionality.
```

We can wrap this verbose one-liner up in a bash function `ask-pkg-json`:
```bash
function ask-pkg() {
  # asks a question of package.json files
  sed '$ s#}$#}\n${1}#' <(jq '.scripts' package.json) | ollama run llama3.2:latest
}
```
and now we can ask all sorts of questions about our package.json scripts portion:

```bash
$ ask-pkg "what kinds of scripts do I use?"
The files and scripts mentioned in the configuration are likely using shell scripting languages, specifically:

1. Bash (sh): The `build.sh` script is used for building different environments (e.g., windows, mac, linux).
2. Shell commands: Other scripts like `shell-commands-to-build-for-test-environment`, `shell-commands-to-build-for-stage-environment`, and 
`shell-commands-to-build-for-prod-environment` likely contain shell commands to configure the build process.

These shell scripts are probably used to automate tasks such as:

* Building the project for different environments (e.g., Windows, macOS, Linux)
* Running tests (unit tests, integration tests, accessibility tests) in various environments
* Configuring the build process for different stages (test, stage, production)

The use of shell scripting languages and commands suggests that the project is using a Unix-like environment or has been set up to work with these 
tools.
```

Since this is a fake package.json in not a real project, I get different results, but I've used this on my own, actual project and it has reduced the amount of difficulty encountered in constructing a query to discover things about my new project. Using an llm as a super-powered `grep` can be useful, when taken with a grain of salt and verified.