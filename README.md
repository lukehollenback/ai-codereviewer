# AI Code Reviewer

AI Code Reviewer is a GitHub Action that leverages OpenAI's large language models to provide intelligent feedback and
suggestions on  your pull requests. This powerful tool helps improve code quality and saves developers time by
automating the code review process.

## Features

- Reviews pull requests using OpenAI's large language models.
- Provides intelligent comments and suggestions for improving your code.
- Filters out files that match specified exclude patterns.
- Supports custom prompting to hone the analysis in on specific things, and avoid others.
- Easy to set up and integrate into your GitHub workflow.

## Setup

1. To use this GitHub Action, you need an OpenAI API key. If you don't have one, sign up for an API key
   at [OpenAI](https://beta.openai.com/signup).

2. Add the OpenAI API key as a GitHub Secret in your repository with the name `OPENAI_API_KEY`. You can find more
   information about GitHub Secrets [here](https://docs.github.com/en/actions/reference/encrypted-secrets).

3. Create a `.github/workflows/main.yml` file in your repository and add the following content:

```yaml
name: AI Code Reviewer

on:
  pull_request:
    types:
      - opened
      - synchronize
      - ready_for_review
permissions: write-all
jobs:
  review:
    if: '! github.event.pull_request.draft'                                       # Only run on non-draft pull requests. Must be coupled with the ready_for_review event above.
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@v3

      - name: AI Code Reviewer
        uses: your-username/ai-codereviewer@main
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}                               # Keep this line verbatim. GitHub sets secrets.GITHUB_TOKEN automatically, it just needs to be plumbed into the action. See https://docs.github.com/en/actions/security-guides/automatic-token-authentication#about-the-github_token-secret for more information.
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}                           # Your OpenAI key. Create one at https://platform.openai.com/api-keys. Set it in your repository under Settings -> Secrets and variables -> Actions -> Repository Secrets.
          OPENAI_API_MODEL: "gpt-4"                                               # Optional: The OpenAI model (https://platform.openai.com/docs/models) to use. Defaults to "gpt-4". Prefer models with JSON Mode (https://platform.openai.com/docs/guides/text-generation/json-mode) support.
          exclude: "yarn.lock, dist/**, **/*.json, **/*.md, **/*.yaml, **/*.xml"  # Optional: File patterns, separated by commas, to exclude from analysis.
          custom_prompts: |                                                       # Optional: Custom commands to add to the prompt used to analyze code. This is a multiline string, where each line is an individual command.
             Do not worry about the verbosity of variable names, as long as they are somewhat descriptive.
             Be sure to call out potential null pointer exceptions.
             Be sure to call out concurrency issues and potential race conditions.
```

4. Replace `your-username` with your GitHub username or organization name where the AI Code Reviewer repository is
   located. If using the [original](https://github.com/freeedcom/ai-codereviewer), replace it with `freeedcom`.

5. Customize the `exclude` input if you want to ignore certain file patterns from being reviewed.

6. Customize or remove the `custom_prompts` input.

7. Commit the changes to your repository, and AI Code Reviewer will start working on your future pull requests.

## How It Works

The AI Code Reviewer GitHub Action retrieves the pull request diff, filters out excluded files, and sends code chunks to
the OpenAI API. It then generates review comments based on the AI's response and adds them to the pull request.

## Tips & Tricks

* The `gpt-4` model is powerful, but [relatively expensive](https://openai.com/pricing) and does not support
  [JSON Mode](https://platform.openai.com/docs/guides/text-generation/json-mode) at the time of writing. Consider using
  one of the turbo models — such as `gpt-4-turbo-preview` or `gpt-3.5-turbo` — to find the right balance between price
  and performance.
* Use `custom_prompts` to hone the action's analysis in on things you care about, and to avoid things you don't. There
  is no point, for example, in having the action repeat what your static analysis tooling does for you in real-time
  while coding.
* Use an `if: '! github.event.pull_request.draft'` job conditional and `ready_for_review` trigger to control costs and
  ensure that you are only running the action on pull requests that are truly ready to be reviewed.

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests to improve the AI Code Reviewer GitHub
Action.

Let the maintainer generate the final package (`yarn build` & `yarn package`).

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
