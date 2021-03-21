Instructions from Corndell workshop (David Wright)

# Jenkins
## Introduction
Jenkins is a tool that facilitates two processes:
Continuous Integration – Code is merged into a main branch and then built and tested automatically.
Continuous Delivery – Production artefacts (e.g. jar files, exe files, etc.) are easily pushed to production environments.

### Continuous Integration
The core principle of continuous integration is that code changes should be merged into the main branch of a repository as soon as possible. Under continuous integration all code branches should be short lived and their changes merged back into the main branch frequently. It favours small, incremental changes over large, comprehensive updates.

Regular code merges reduce the risk of two developers changing the same code at the same time. This reduces both the number and the severity of merge conflicts.  CI also speeds up development.  Merging your code changes into the main branch regularly makes your code changes available to other developers as soon as possible so they can build upon your work.

### Continuous Delivery
Continuous delivery (CD) is about building pipelines that automate software deployment.  Continuous delivery pipelines can be built as standalone tools, but are most often built on top of CI pipelines: if your code passes all the tests, the CI/CD platform builds/compiles/ bundles it, and its dependencies, into an easily deployable build artefact which is stored somewhere for easy access during future deployments. At the click of a button, the CD pipeline can deploy the artefact into a target environment.

## Jenkins Setup
Normally Jenkins would run on a dedicated machine, but in order to facilitate learning we need a version that we can start, stop and reconfigure on our terms.  Therefore we’re going to run a Jenkins server on our local machines.

<ul>
<li>Download and install Jenkins from the Jenkins download page (https://www.jenkins.io/download/).  Get the Generic Java package (.war).  While it’s possible to run it in the background as, for example, a Windows service, we’re going to use the jar file so that we can see what’s going on behind the scenes a bit more easily.</li>
<li>Put the downloaded .war file into its own folder, then run it manually using the instructions on the Getting Started page (https://www.jenkins.io/doc/pipeline/tour/getting-started/).  Specifically, run:</li>
<li>java -jar jenkins.war --httpPort=8080</li>
<li>then browse to http://localhost:8080.</li>
<li>After installation you should be prompted to copy/paste the initial admin password from the given location.</li>
<li>Install the suggested plugins.  We’re going to also use the GitHub plugin but will add it later.</li>
<li>You can skip creating an admin user.</li>
</ul>

## Project Setup
We will create a simple project using JavaScript (don’t worry if you’re unfamiliar with the language!) and then get Jenkins to build, test and deploy it.  However, first we need a project that we can even run on our own machines!

### Installing node, npm and git
If you don’t already have them installed, install nodejs and git.  Installing node should also install npm.  You can check whether they have installed correctly by typing:
<br>node -v
<br>npm -v
<br>git --version

### Creating a new node project
Create a new folder called JenkinsTestProject, open a Git Bash window at that location and create a new project by typing npm init.  You can accept all the default options.  Also initialise a git repository at the location by typing git init.  Also create a new public repository at GitHub and push your new project to it.

### Writing some dummy code
Create a new file called main.js and copy/paste the following line of code.
console.log('Hello world!');
You should then be able to run node main.js from the Git Bash window and see it output the message.

## Getting Jenkins to build the project
By this point you should have:
<ul>
<li>Jenkins, node, npm and git installed</li>
<li>Jenkins running and visible at http://localhost:8080</li>
<li>A project which prints, “Hello world!” and has been pushed to a location on GitHub.</li>
</ul>
We now need to tell our Jenkins server about our project on GitHub.

### Installing the GitHub plugin
Follow the instructions on the Jenkins website about how to install a plugin.  I recommend using the web UI to do this.  The plugin you’re looking for is called GitHub and should match the most recent version listed on the GitHub plugin website (https://plugins.jenkins.io/github/).  You will most likely need to restart the server afterwards.

### Pipelines
Pipelines are the normal way of working with Jenkins.  Code goes in one end, build artefacts come out the other end and various code building, testing and validation is done in the middle.  We’re going to create a build pipeline for our little project.

### Creating a new project
<ul>
<li>On the web UI click New Item and create a new Pipeline project.</li>
<li>Click the “GitHub project” button and give it the URL of your GitHub project.</li>
<li>Save your changes.</li>
</ul>

Now, on your Dashboard you should see your project.  It won’t yet build though as we haven’t yet added an actual pipeline to it.  We’re going to create a pipeline using a Jenkinsfile as this is much more common than putting the pipeline code directly into the configuration.

<ul>
<li>Have a look at Getting Started (https://www.jenkins.io/doc/book/pipeline/getting-started/) with Pipeline.  We will be following some of the instructions in the second part of the document.</li>
<li>Create a new file called “Jenkinsfile” (no file extensions like .txt needed).  This should be at the root of your project.</li>
<li>Copy the contents of the ‘Hello World’ example Jenkinsfile from the link above and paste it into your new Jenkinsfile.</li>
<li>Commit and push your changes to GitHub.</li>
</ul>

At this point your build is ready to do something, but it won’t do so because we haven’t told Jenkins to use the Jenkinsfile given in your project.

</ul>
<li>Go into your project on the Jenkins UI and click Configure.</li>
<li>At the bottom under Pipeline choose “Pipeline script from SCM”.</li>
<li>Put in your repository URL and, if needed, change the branch specifier from */master to */main.  You shouldn’t need any credentials if you made if a publicly visible project.</li>
<li>Save your changes and click Build Now.</li>
<li>The project should build fairly quickly.  You can then look at the console output by clicking the build number (e.g. #1) and then choosing Console Output on the left hand side.  You’ll also see a navigation section at the top of the page, e.g. Dashboard > MyJenkinsProject > #1.</li>
</ul>

### Snippets
Jenkins has a built in snippet generator for creating parts of a Jenkinsfile.  You can find it by going to your pipeline configuration and then going all the way to the bottom where you’ll find a hyperlink to Pipeline Syntax.

Using the Pipeline Syntax helper, modify your Jenkinsfile so that it performs the following operations in this order:
<ul>
<li>Prints “Beginning build process” to the console</li>
<li>Sleeps for 2 seconds</li>
<li>Verifies that the file main.js does exist</li>
<li>Asks the user, “Do you want to run main.js?” and waits for a response</li>
<li>Runs “node main.js” as a shell script command</li>
</ul>

Once you’ve added everything, go back to the web UI and click Build Now to build it again.

### Automated builds
A Jenkins pipeline can be run manually as you have been doing.  However, it’s much more normal for the pipeline to automatically run every time you push a change to your GitHub repo.  To enable this we need to go to GitHub and create a webhook.  Jenkins can then use that webhook to receive a notification each time something changes.

The problem is that GitHub is out there on the internet, but your Jenkins server is running on localhost.  So we can’t tell GitHub where on the internet to find your Jenkins server.  Fortunately, there’s a tool call ngrok (https://ngrok.com/) which can help here.

<br>
Step 1 – Creating a link with ngrok
<ul>
<li>Create an account with ngrok and download the executable.</li>
<li>Run it to create link to your localhost:8080 by running ngrok http 8080 (assuming your Jenkins server is running on port 8080).</li>
<li>Check that the link is working by going to http://######.ngrok.io.  You should see your localhost server running via the ngrok link.</li>
</ul>
<br>
Step 2 – Enable the GitHub plugin in Jenkins
<ul>
<li>Go to your Pipeline configuration</li>
<li>Check the box that says, “GitHub hook trigger for GITScm polling”</li>
</ul>
<br>
Step 3 – Set up the webhook in GitHub
Now that your Jenkins server is visible on the internet you can tell GitHub to push notifications to it.
<ul>
<li>Go to your GitHub repo and click Settings  Webhooks.</li>
<li>Create a webhook equal to your ngrok link plus /github-webhook, for example http://123456abcdef.ngrok.io/github-webhook/</li>
</ul>
N.B.: The trailing / is very important.  If you leave it off it will not work.

Now, when you commit and push a change to your repo you it should automatically run the pipeline.

## Other topics to investigate
By this point you have successfully set up a Jenkins server which has a project pipeline set up and will automatically run the pipeline whenever you run git push.  Some other topics you could investigate independently:
<ul>
<li>How to set environment variables differently, so for example when you run node main.js from your own machine it prints, “Hello development world!” but when Jenkins runs it then instead it prints, “Hello release world!”</li>
<li>How to store passwords securely on different environments, so that developers never get access to the passwords for the production environment.</li>
<li>How to manage multiple users so that only some users get access to your project.</li>
</ul>