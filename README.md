Repository containing all examples and notes for the Github actions course

# YAML
Indentation is important 

# Workflow, Jobs, steps
Jobs run in parallel by default
steps run sequentially by default
Jobs run in independent VM

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

