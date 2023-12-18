## srcwarnings-ci-action

Latest version: v0.0.1

This action wraps Dedaub's [srcwarnings](https://github.com/Dedaub/srcwarnings) tool as a github action so that it can be easily used 
in workflows

### Usage
See [action.yml](https://github.com/Dedaub/srcwarnings-ci-action/blob/main/action.yml)

### Inputs

The action expects a number of inputs:
* project_id: The id of the project for which we want the warnings
* version_id: The version of the project for which we want the warnings
* timeout: (optional) The timeout (in secs) after which the action will stop polling for warnings. Default is 300 seconds
* period: (optional) The polling period (in secs). Default is 30 seconds
* api-key: (optional) The key is optional if you configured the runner to use a configuration file. Otherwise needs to be provided

### Outputs

The action produces one output: `project_report`

### Example

The following is an example of how the action can be used in a workflow (together with its sister action `srcup-ci-action`

```
      - name: Run srcup
        id: srcup
        uses: dedaub/srcup-ci-action@v0.0.1
        with: 
          name: 'cool-project-name'
          api-key: ${{ secrets.WATCHDOG_SECRET }}
          framework: 'Hardhat'
          location: staking-v0.1
      - name: Get analysis summary
        id: get_project_report
        uses: dedaub/srcwarnings-ci-action@0.0.1
        with:
          project-id: ${{ steps.srcup.outputs.project_id }}
          version-id: ${{ steps.srcup.outputs.version_id }}
          api-key: ${{ secrets.WATCHDOG_SECRET }}
      - name: Print the info
        run: | 
          echo ${{ steps.get_project_report.outputs.project_report }}
          echo ${{ steps.get_project_report.outputs.project_report }} >> $GITHUB_STEP_SUMMARY
        shell: bash
```