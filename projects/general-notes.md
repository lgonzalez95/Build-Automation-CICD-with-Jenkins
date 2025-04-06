### What is the build automation process?
1. Test Code
2. Build application
3. Push to repository
4. Deploy to a server

### What do we need to configure in Jenkins?

1. Install tools available to run the tests: npm, gradlew, mvn
2. Configure test environment: Test DB for example
3. Install docker if required
4. We need a way to store docker credentials

### Credentials Type

1. System Credentials: Only available on Jenkins server, pipelines cannot see these type of credentials.
2. Global Credentials: Are accessible everywhere.