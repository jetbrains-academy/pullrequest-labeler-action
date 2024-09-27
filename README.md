# PullRequest labeler action
Action for hanging labels depending on the [GH team](https://github.com/orgs/jetbrains-academy/teams) of the person who approved the Pull request.

## Logic
- If a review from a team member has been requested, that team's label is removed.
- If an approval was received from all requested group members, the label of that group is hanged.

## Inputs
### github-token
Token with the following permissions:  
- **Repository permissions**
  - `Metadata: Read-only`
  - `Pull requests: Read and write`
  - `Secrets: Read-only`
- **Organization permissions**
  - `Custom organization roles - Read-only`
  - `Members - Read-only`
  - `Secrets: Read-only`
### rules
A list of pairs that determines for which team which label should be hanged in the format:
```
[["team1", "labelname1"], ["team2", "labelname2"]]
```

## Usage example
```yaml
name: Approve labeler
on:
  pull_request_review:
  pull_request:
    types: [ review_requested ]

jobs:
  approve_labeler_job:
    runs-on: ubuntu-22.04
    name: Approve labeler
    steps:
      - name: Generate a github token
        id: generate-github-token
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ vars.PR_LABELER_ID }}
          private-key: ${{ secrets.PR_LABELER_PRIVATE_KEY }}
      - name: Calling PullRequest review labeler
        uses: jetbrains-academy/pullrequest-labeler-action@main
        with:
          github-token: ${{ steps.generate-github-token.outputs.token }}
          rules: '[["proofreaders", "proofread"], ["testers", "tested"]]'
```
