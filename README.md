# GitHub Actions Demo
Simple Spring Boot application (v2.7.0) built on Java 11 and Maven to demonstrate the use of Github Actions.
## Description
This project has GitHub Actions workflow in a single yaml file, containing a sequence of jobs that perform the following tasks when a commit is pushed to the main branch:
### Initialization:
* Uses anothrNick/github-tag-action to determine the next tag version.
    * The project follows semantic versions (major.minor.patch) and the default next version to update is set to 'patch'.
    * For example, if current version tag is set to 1.0.0 the next default version will be 1.0.1.
    * To change this to update major or minor versions, set DEFAULT_BUMP to 'major' or 'minor' in the workflow.
* The current & next versions are stored as an output for later use
### Build:
* Runs maven build to verify that project builds successfully after changes
### Publish:
* Sets project version to next version determined in Initialization stage.
* Publishes application to GitHub packages.
### Create_release:
* Uses elgohr/Github-Release-Action to create a GitHub release whose title matches the version determined in Initialization stage.
* Uses anothrNick/github-tag-action to create a tag

***Note: The workflow doesn't execute if a commit is pushed to any other branch***

## Links
- [Project Repository](https://github.com/imranKpathan/devops-challenge "GitHub Actions Demo Repo")
- [Github Actions workflow for this project](https://github.com/imranKpathan/devops-challenge/blob/main/.github/workflows/github-actions-workflow.yml)
- [GitHub Packages Registry for this project](https://github.com/imranKpathan/devops-challenge/packages/1476029/versions "Link to GitHub Packages")
- [GitHub releases and tags for this project](https://github.com/imranKpathan/devops-challenge/releases "Link to GitHub Releases")
## Built With
- [Spring Boot](https://start.spring.io/ "Spring Boot v2.7.0 app with Java 11 & Maven")
