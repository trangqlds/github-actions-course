Repository containing all examples and notes for the Github actions course

# YAML
Indentation is important 

# Workflow, Jobs, steps
Jobs run in parallel by default
steps run sequentially by default
Jobs run in independent VM, steps share VM

# if singe step fail, all subsequent steps will fail


# use this to execute a mullti-line bash script
 run: |
          echo  "I will succeed"
          exit 0

# to access the name of event triggering the workflow: ${{github.event_name}}
# Using a valid cron syntax

At the time of this recording, GitHub Actions does not support cron job definitions containing six elements (for example, '0 0 * * * *'), only definitions containing five elements. Check the resources section of this lecture for a cron generator that uses the valid syntax.

cron expression generator http://crontab.cronhub.io/

To define a trigger using cron, you should use the following syntax:

on:
  schedule:
    - cron: '<cron expression>'


 # different way to list triggers
on:
    - push
    - pull_request

OR 
    push:
    pull_request:


# Note: 
-  Keep the VM resources in mind, especially when running commands that rely on parallel execution (for example, running parallel jest tests): 
For example, running unit test on our machine well, but when push it and run on CI/CD pipeline, we run into the resource limitation of our githhub-hosted VM
 - Do not use self-hosted runners in public repositories! as they are more prone to attack

- to know if bash or any software is aviable in a runner: got to the jobs on github action -> set up jobs -> runner image -> included software
Be careful with MacOS runners, they are expensive!

- MacOS runners are expensive when used in private repositories, and they can easily consume all the free minutes we have available for the month! Be careful if you are running your workflows in a private repository.

- How to access the runner OS

The runner OS is available as an environment variable named $RUNNER_OS.

Accessing environment variables in Windows

Window's default shell is not compatible with bash-like syntax for accessing environment variables. You can either use a compatible method, or use bash by explicitly setting the shell for the respective step:

steps: 
  - name: Show OS 
    shell: bash 
    run: echo "I'm running on bash."

# Actions: wrap up commands that we use repeatedly in actions 
actions marketkplace to use 3rd party custom actions, select one with badget verified by GitHub
https://github.com/marketplace?type=actions

- to run independence in a directory, 2 ways:
 - name: Install Dependencies
    run: |
        cd 04-using-actions/react-app
        npm ci

  OR
    run: npm ci
    working-directory: 04-using-actions/react-app

- to set a default directory for all runs: using defaults
jobs: 
    build:
        runs-on: ubuntu-latest
        defaults:
            run:
                working-directory: 04-using-actions/react-app
        steps:

# Event filters
- If multiple filters are specified, all of them must be satisifed for the workflow to run
https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#onpushbranchestagsbranches-ignoretags-ignore

# aCtivity types
- triggers can have different activity types 
- specify which activities of certatin triggers execute our workflow
check this docs
https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows


# Contexts
https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs#about-contexts

## run-name: define the name of the workflow run that appears on the UI
name: 06 - Contexts

run-name: 6 - Contexts | DEBUG - ${{ inputs.debug }}

on:
    push:
    workflow_dispatch:
      inputs:
        debug:
          type: boolean
          default: false

# expression can be used to reference information from multiple sources within the workflow
${{ <expression> }}


Conditionally executing jobs and steps

To execute jobs and steps conditionally, we can leverage the if keyword in either the job or the step definition. The if keyword accepts an expression, and the job or step will be executed if the expression evaluates to a truthy value; otherwise, the job or step will be skipped. Here is an example:

jobs: 
  echo: 
    runs-on: ubuntu-latest
    steps:
      - name: Test
        if: github.event_name == 'push'
        run: echo "I was triggered by a push event"
Note: if is already an expression, so there is no need to do this: ${{ github.event_name == 'push' }}

## Expressions allow for default values and ternary operations. These leverage the && and || logical operators in the following way:

Providing a default: ${{ expression || default_value }}. The default_value will be used if the expression evaluates to a falsy value.

Using the ternary operation: ${{ expression && truthy_value || falsy_value }}. The truthy_value will be used if the expression evaluates to a truthy value, and the falsy_value will be used otherwise. The actual value resulting from the expression evaluation will not be present in the result of this ternary operation.

# VARIABLES
- Set and reuse non-sensitive configuration information
- Single workflow: can be accessed like $var_name or ${{ vars.name }}
  $var_na,me: wont be replaced before execution while ${{ }} will be 
- Mutiple workflow: need to access by using expression: ${{ vars.name }}
- Note: to avoid errors when recalling from shell script, avoid using '-' in environment variable name, use  underscore _ instead
  for example: var_step2 instead of var-step2
  For job/step name: use '-' (???)

- job/step level env variable is only available at job/step level but workflow env level is available at workflow level, job and step level

- Note: these keywords
  * env: to define variables at worklfow/jobs/step levels
  * environment : to define what environment a sjob should run, for example, production, staging, development

# Function
- general purpose function
- Status check function: success, failure, always, cancelled ( !cancelled : not cancelled)
Note: recommended to use !cancelled() instead of always to excecute jobs or steps even if previous jobs or steps failed, but to prevent execution in case the worfklow is cancelled

Check this doc:
https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/evaluate-expressions-in-workflows-and-actions
Printing multi-line JSON strings

To print a multi-line JSON string from the toJSON GitHub function, you can use the following pattern:
steps:
  - name: Print PR labels
    run: | 
      cat << EOF
      ${{ toJSON(github.event.pull_request.labels) }}
      EOF

# Controlling the execution flow: 
- all jobs are executed in parellel by default (steps are run sequentially already). Use keyword "need" to execute JOBS in sequence
10-execution-flow.yaml is quite similar  to a real CI/CD worflow will be like. 
- When specifying inputs for a trigger, could use  "description" to create a tick box when manually trigger in UI:
    on:
    workflow_dispatch:
        inputs:
            pass-unit-tests:
              type: boolean
              description: whether unit tests will pass or not
              default: true

- to refer to inputs specified for a trigger, use ${{ inputs.your_input_name }}
  steps:
    - name: Failing tests
      if : ${{ !inputs.pass-unit-tests }}
      run: exit 1

- continue-on-error: true => this could be used to experiement. When using it, even if a job fails, sequential jobs will still succeed and the whole workflow still suceed. Check this https://github.com/trangqlds/github-actions-course/actions/runs/13609445505

# Inputs and outputs
- Input provide information to customize workflows and actions:
  * workflow_dispactch 
  * workflow-call: to build reusable workflow: need to provide inputs
  * when creating a custom actions: 
3 types for inputs for triggers: boolean, choice (need to provide "options") and environment

- Outputs: use output data from previous jobs for later usage
Not suitable if have to share more complex data or a set of files, then artifacts are better 
  1. Need to provide id for jobs htaht we want to produce outputs 
  2. Echo key-value pairs the $GITHUB_OUTPUT varaiable (it holds path to a certain file used by github to process the output of it setep)
    need to use ">>" as if use echo with 1 ">", it will overwrite the file and lose previous outputs
  3. Mention the outputs in the outputs section of the JOB (NOT STEPS)
  4, 5 ...

NOTE: referring to 12-output.yaml
 * to write the output of a step to GITHUB_OUTPUT, make sure no space before ". For example:
    run: echo "status=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"
 * in the outputs keywork for a job:
    outputs:
      build-status-output : ${{ steps.<step-id>.outputs.<name> }}
    => step id is id of the step that produce the outputs in that job
    => name: is from echo "<name>=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"

* a job Should execute only after the "build" job successfully completes and if the "status" output of the build job equals success: 
    needs: build
    if: ${{ steps.build.outputs.build-status == 'success' }}

NOTE: referring to 12-output.yaml
 * to write the output of a step to GITHUB_OUTPUT, make sure no space before ". For example:
    run: echo "status=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"
 * in the outputs keywork for a job:
    outputs:
      build-status-output : ${{ steps.<step-id>.outputs.<name> }}
    => step id is id of the step that produce the outputs in that job
    => name: is from echo "<name>=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"

* a job Should execute only after the "build" job successfully completes and if the "status" output of the build job equals success: 
    needs: build
    if: ${{ steps.build.outputs.build-status == 'success' }}

NOTE: referring to 12-output.yaml
 * to write the output of a step to GITHUB_OUTPUT, make sure no space before ". For example:
    run: echo "status=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"
 * in the outputs keywork for a job:
    outputs:
      build-status-output : ${{ steps.<step-id>.outputs.<name> }}
    => step id is id of the step that produce the outputs in that job
    => name: is from echo "<name>=${{ inputs.build-status }}" >> "$GITHUB_OUTPUT"

* a job Should execute only after the "build" job successfully completes and if the "status" output of the build job equals success: 
    needs: build
    if: ${{ steps.build.outputs.build-status == 'success' }}