## srcwarnings-ci-action

Latest version: v0.0.1

This action wraps Dedaub's [srcwarnings](https://github.com/Dedaub/srcwarnings) tool as a github action so that it can be easily used
in workflows

### Usage

See [action.yml](https://github.com/Dedaub/srcwarnings-ci-action/blob/main/action.yml)

### Inputs

The action expects a number of inputs:

- `project-id`: (required) The id of the project for which we want the warnings
- `version-id`: (required) The version of the project for which we want the warnings
- timeout: (optional) The timeout (in secs) after which the action will stop polling for warnings. Default is 300 seconds
- period: (optional) The polling period (in secs). Default is 30 seconds
- `api-key`: (required) Your Dedaub API key. Pass it through a GitHub Actions secret
- `api-url`: (optional) The API endpoint. Defaults to `https://api.dedaub.com/api`

### Outputs

The action produces one output: `project_report`

### Example

The following steps upload a project with `srcup-ci-action`, retrieve its analysis
summary, and write the report to the workflow's job summary.
Add them after checking out the repository and installing the project's dependencies.
Replace `staking-v0.1` with your project directory and configure the
`WATCHDOG_SECRET` repository secret with your Dedaub API key.

The upload action exposes underscore-separated output names (`project_id` and
`version_id`). This action accepts those values through hyphen-separated inputs
(`project-id` and `version-id`). Do not use the output names as input keys in `with`.

```yaml
- name: Run srcup
  id: srcup
  uses: dedaub/srcup-ci-action@v0.0.2
  with:
    name: "cool-project-name"
    api-key: ${{ secrets.WATCHDOG_SECRET }}
    framework: "Hardhat"
    location: staking-v0.1
- name: Get analysis summary
  id: get_project_report
  uses: dedaub/srcwarnings-ci-action@v0.0.1
  with:
    project-id: ${{ steps.srcup.outputs.project_id }}
    version-id: ${{ steps.srcup.outputs.version_id }}
    api-key: ${{ secrets.WATCHDOG_SECRET }}
- name: Print the info
  env:
    PROJECT_REPORT: ${{ steps.get_project_report.outputs.project_report }}
  run: |
    printf '%s\n' "$PROJECT_REPORT"
    printf '%s\n' "$PROJECT_REPORT" >> "$GITHUB_STEP_SUMMARY"
  shell: bash
```
