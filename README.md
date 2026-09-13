# Quattera — run tests (GitHub Action)

Triggers a [Quattera](https://quattera.ai) test run on your always-on Quattera runner, waits for it, prints the result in the job summary, and fails the job when the run finds bugs.

## Usage

```yaml
- uses: quattera-ai/run-tests@v1
  with:
    api-token: ${{ secrets.QUATTERA_API_TOKEN }}
    project: my-app            # project name or id
    list: nightly-smoke        # test list name or id
    runner: ci-runner-1        # runner name from Settings → CI/CD
```

Create the token in Quattera → **Settings → CI/CD → API tokens** and store it as the repository secret `QUATTERA_API_TOKEN`.

## Inputs

| input | required | default | description |
|-------|----------|---------|-------------|
| `api-token` | yes | — | Quattera API token (from a secret) |
| `project` | yes | — | project name or id |
| `list` | yes | — | test list name or id |
| `runner` | yes | — | runner name |
| `version` | no | branch/tag name | version tag stored on the run |
| `wait` | no | `true` | wait for the run and fail the job by its result |
| `timeout-minutes` | no | `60` | give up waiting after this long (run keeps going) |
| `api-url` | no | `https://app.quattera.ai` | self-hosted / testing only |

## Outputs

`run-id`, `run-url`, `status` — use them in later steps, e.g. to post the link to Slack or a PR comment.

## Result

| job result | meaning |
|------------|---------|
| success | run passed, no bugs |
| failure (exit 1) | bugs found, failed scenarios, needs data fix / remap, page error |
| failure (exit 2) | no verdict: runner offline or busy, token rejected, timeout, run cancelled |

Runs from the same workflow execution are grouped in Quattera as one build (`github.run_id`), so parallel jobs on different runners show as one row.

This action installs [`quattera-cli`](https://pypi.org/project/quattera-cli/) and runs `quattera run … --wait --github`. Jenkins, GitLab and others use the CLI directly.