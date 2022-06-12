# GitHub Actions Demo
Simple Spring Boot application (v2.7.0) built on Java 11 and Maven to demonstrate the use of Github Actions.
## Links
- [Project Repository](https://github.com/imranKpathan/devops-challenge "GitHub Actions Demo Repo")
- [Github Actions workflow for this project](https://github.com/imranKpathan/devops-challenge/blob/main/.github/workflows/github-actions-workflow.yml)
- [GitHub Packages Registry for this project](https://github.com/imranKpathan/devops-challenge/packages/1476029/versions "Link to GitHub Packages")
- [GitHub releases and tags for this project](https://github.com/imranKpathan/devops-challenge/releases "Link to GitHub Releases")
## Built With
- [Spring Boot](https://start.spring.io/ "Spring Boot v2.7.0 app with Java 11 & Maven")
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

_NOTE: The workflow doesn't execute if a commit is pushed to any other branch._

## Workflow File:
```Workflow
name: GitHub Actions Demo
on:
  push:
    branches: [ "main" ]
jobs:
  Initialization:
    runs-on: ubuntu-latest
    outputs:
      next_version: ${{ steps.DetermineNextTagVersion.outputs.new_tag }}
      current_version: ${{ steps.DetermineNextTagVersion.outputs.tag }}
    steps:
      - uses: actions/checkout@v3
      - name: Determine Next Tag Version
        id: DetermineNextTagVersion
        uses: anothrNick/github-tag-action@1.39.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          VERBOSE: true
          DRY_RUN: true
          DEFAULT_BUMP: 'patch'
  Build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'adopt'
          server-id: github
      - name: Build with Maven
        run: mvn clean install
  Publish:
    runs-on: ubuntu-latest    
    needs: [Initialization, Build]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Set Release version
        run: mvn versions:set -DnewVersion=${{needs.Initialization.outputs.next_version}}
      - name: Publish to GitHub Packages Apache Maven 
        run: mvn --batch-mode -DuseGitHubPackages=true deploy
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  Create_Release:
    runs-on: ubuntu-latest   
    needs: [Initialization, Publish]
    steps:
    - uses: actions/checkout@v3 
    - name: Print Next Tag
      run: echo ${{needs.Initialization.outputs.next_version}}
    - name: Create a Release  
      uses: elgohr/Github-Release-Action@v4
      env:
        GITHUB_TOKEN: ${{ secrets.IMK_RELEASE_TOKEN }}
      with:
        title: ${{needs.Initialization.outputs.next_version}}
    - name: Tag Version 
      uses: anothrNick/github-tag-action@1.39.0
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        DEFAULT_BUMP: patch
    - name: Tag Version 
      uses: anothrNick/github-tag-action@1.39.0
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        DEFAULT_BUMP: 'patch'
```
