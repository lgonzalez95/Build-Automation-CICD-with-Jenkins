# Demo Project:
Create a Jenkins Shared Library

### Technologies used:
Jenkins, Groovy, Docker, Git, Java, Maven

### Project Description:
Create a Jenkins Shared Library to extract common build
logic: 
1. Create separate Git repository for Jenkins Shared Library project
2. Create functions in the JSL to use in the Jenkins pipeline
3. Integrate and use the JSL in Jenkins Pipeline (globally and for a specific project in Jenkinsfile)


## Create separate Git repository for Jenkins Shared Library project

1. We need to create a separate groovy project using Intellij.
2. Create a `vars` folder where the functions will be located.
3. Inside `vars` create files having the same name as the functions we had in the `script.groovy` before, for example `buildImage.groovy`
4. Paste the code we had in the script as well but inside a call function, for example `def call() { CODE }`
5. There might be other folders like `src` for helper code and `resources` for external libs and non groovy files.
6. Initialize the repo and push it to GitHub/GitLab.
7. In Jenkins go to manage Jenkins -> System -> Global Trusted Pipeline Libraries
8. Add a new trusted library, any name is fine select a branch or specific version and add the GIT credentials
9. Save.
10. In the project Jenkinsfile, at the start of the file add `@Library('jenkins-shared-library')`, in case we don't have any code line between `@Library('jenkins-shared-library')` and `pipeline {  ` we need to add a `_` after the parenthesis so it would look like this `@Library('jenkins-shared-library')_`
11. In the pipeline steps, just call the functions  by using the groovy file name, for example `buildImage()`
12. If the function receives params call them as `buildImage 'param'`

## Notes
1. We can have the library referenced in the Jenkinsfile if we just use it for a couple of projects, use the syntax: 
```
library identifier: 'jenkins-shared-library@master',
retriever: modernSCM([
    $class: 'GitSCMSource',
    remote: 'https://github.com/your-org/your-shared-lib.git',
    credentialsId: 'your-credentials-id'
])
```