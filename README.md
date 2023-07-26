# Github Actions

## Structure

![diagram](https://docs.github.com/assets/cb-25535/mw-1440/images/help/actions/overview-actions-simple.webp)


### Workflows 

* Workflows are automated processes defined by a YAML file in a repository.
* They can be triggered by events, manual actions, or on a schedule.
* Multiple workflows in a repository can perform different tasks, and they can reference each other.

### Events

* Events in a repository trigger workflow runs.
* Examples of events include pull request creation, issue opening, and commit pushing.
* Workflows can also be scheduled, triggered via REST API, or run manually.

### jobs

* A job in a workflow consists of steps executed on the same runner.
* Steps can be shell scripts or actions, executed in order and dependent on each other.
* Jobs can have dependencies on other jobs, allowing them to wait for dependent jobs to complete before running. This enables parallel execution and coordination between tasks.

### Actions 

* Actions are custom applications for GitHub Actions that handle complex and repetitive tasks.
* They help reduce repetitive code in workflow files.
* Actions can perform tasks like pulling repositories, configuring build environments, and setting up authentication for cloud providers. They can be authored by users or found in the GitHub Marketplace for reuse.

[Action Marketplace](https://github.com/marketplace/actions/)

### Runners

* Runners are servers that execute workflows when triggered.
* Each runner can handle one job at a time.
* GitHub provides Ubuntu Linux, Microsoft Windows, and macOS runners in fresh virtual machines, and larger configurations are available too.
* Users can also set up their own runners for different OS or specific hardware requirements

## Examples

### Variables

Create custom variables named POSTGRES_HOST and POSTGRES_PORT. These variables are then available to the node client.js script.

```yml
jobs:
  example-job:
      steps:
        - name: Connect to PostgreSQL
          run: node client.js
          env:
            POSTGRES_HOST: postgres
            POSTGRES_PORT: 5432
```


### Scripts

#### Inline

```yml
jobs:
  example-job:
    steps:
      - run: npm install -g bats
```

#### File 

```yml
jobs:
  example-job:
    steps:
      - name: Run build script
        run: ./.github/scripts/build.sh
        shell: bash
```

### Sharing data

#### Using artifacts

Save an artifact

```yml
jobs:
  example-job:
    name: Save output
    steps:
      - shell: bash
        run: |
          expr 1 + 1 > output.log
      - name: Upload output file
        uses: actions/upload-artifact@v3
        with:
          name: output-log-file
          path: output.log
```

Consume an artifact

```yml
jobs:
  example-job:
    steps:
      - name: Download a single artifact
        uses: actions/download-artifact@v3
        with:
          name: output-log-file
```

#### Using Github Output

```yml
first_job: 
  steps:
    - name: Produce some output
      id: step-id
      run: echo "VAR=foobar" >> $GITHUB_OUTPUT

  outputs:
    job_output: ${{ steps.step-id.outputs.VAR }}

second_job:
  needs:
  - first_job
  steps:
  - name: Use output
    run: echo $VAR
    env:
      VAR: ${{ needs.first_job.outputs.job_output }}
```

### Parallel operations

#### Matrix

```yml
jobs:
  example_matrix:
    strategy:
      matrix:
        version: [10, 12, 14]
        os: [ubuntu-latest, windows-latest]
```
