Practical Exercise 04 - Working with Third-Party Custom Actions
Exercise Description
In this practical exercise, our goal is to explore how we can use third-party custom actions to perform tasks without having to define them from scratch.

In order to achieve that, we will leverage a React application that we will scaffold with the help of the create-react-app utility. Check the tips section for the specific command to scaffold your React application. Here are the instructions for the exercise:

Generate a React application:

Create a new folder named 04-using-actions at the root of the repository.

Using a terminal, cd into this directory and scaffold a React application inside a react-app directory. You can either create the directory yourself or let the create-react-app utility do it for you.

Once the React setup is done, you should see a success message.

Take a few moments to inspect the files and get familiar with the application folder structure.

Create the first version of the workflow:

Create a file named 04-using-actions.yaml under the .github/workflows folder at the root of your repository.

Name the workflow 04 - Using Actions.

Add the following triggers to your workflow:

push

Add a single job named build to the workflow. The job should contain two steps:

The first one, named Checkout Code, should checkout the repository code into the current working directory.

The second one, named Printing Folders, should simply print the folder structure after the checkout command.

Commit the changes and push the code.

Take a few moments to inspect the output of the workflow run.

Extend the workflow to setup node and install the dependencies of the React application

Remove the Printing Folders step.

Add a new step after the Checkout Code step. This new step should be named Setup Node and setup node using the 20.x version.

Add a new step after the Setup Node step. This new step should be named Install Dependencies install the dependencies of our React application by running the npm ci command inside the React application folder. You can either cd into the directory before running the npm ci command, or you can pass the working directory by adding a working-directory: 04-using-actions/react-app option to the step (as a sibling key to name and run).

Commit the changes and push the code.

Take a few moments to inspect the output of the workflow run.

Extend the workflow to execute the automated tests from the React application:

Add a new step after the Install Dependencies step. This new step should be named Run Unit Tests and it should execute the automated tests by running the npm run test command inside the React application folder. You can either cd into the directory before running the npm run test command, or you can pass the working directory by adding a working-directory: 04-using-actions/react-app option to the step (as a sibling key to name and run).

Commit the changes and push the code.

Take a few moments to inspect the output of the workflow run.

Change the workflow triggers to contain only workflow_dispatch to prevent this workflow from running with every push and pollute the list of workflow runs.

Tips
Scaffolding a React application with create-react-app

To generate a React application with a single command, you can leverage the create-react-app utility. In order to do that, simply run the following inside the 04-using-actions folder: npx create-react-app --template typescript react-app.

Using third-party actions in GitHub Actions Workflows

The syntax to leverage a third-party action is very simple. Instead of using the run key and defining a shell script, you should use the uses key and pass the name and the version of the action you wish to use. Here is an example of the syntax:

steps:
  - name: Using the Checkout Action
    uses: actions/checkout@v4
Useful third-party actions for this exercise

actions/checkout@v4: used to checkout the repository code into the working directory of the workflow run. Without this, we cannot work with the code from our repository.

actions/setup-node@v4: used to setup node with a specific version, as well as any other necessary dependencies. To provide a specific version of Node to be setup, you can use the following syntax:

steps:
  - name: Setup Node
    uses: actions/setup-node@v4
    with:
      node-version: '20.x'
# Custom Actions
- 3 types of custom actions: composite actions, javascript & docker actions
 * Composite actions: simplest type of custom actions, is a grouping of other github actions, so may not be enough for complex functinoality
 * Javascript: allow writing any type of custom logic, @actions package, but need Javascript knowledge and node environment
 * Docker: similar to Javascript but can be written in any programming language (Python, etc.) and  might be more verbose
 All types requires action.yaml & must be on its own repo if its to be reused by other repos (dosnt contain other custom actions)