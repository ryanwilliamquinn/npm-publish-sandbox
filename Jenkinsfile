node {
    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        inBuildAgent 'npm prune; npm install; npm run build'
    }

    stage('Unit-Test') {
        inBuildAgent 'npm test'
    }

}


echo env.BRANCH_NAME
if (env.BRANCH_NAME == "master") {
    
    try {
        stage('User Input') {
        def userInput = input(
            id: 'semverTypeInput',
            message: 'Shall we publish?',
            ok: 'Yes go do it',
            parameters: [choice(choices: "patch\nminor\nmajor", description: 'semver types', name: 'semverType')],
            submitterParameter: 'versionType')
        }

        node {
            stage('Deploy') {
                inBuildAgent 'echo ${env.NPM_AUTH_DATA} > ~/.npmrc'
                inBuildAgent 'git remote -v'
                inBuildAgent 'git config --global user.email "jenkins@mydomain.com"'
                inBuildAgent 'git config --global user.name "jenkins"'
                inBuildAgent 'npm version patch; npm publish'
                inBuildAgent 'git push origin master'
                inBuildAgent 'git push origin --tags'
            }
        }
    } catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException fe) {
        echo("aborted")
    } catch (error) {
        throw error
    }
}

def inBuildAgent(command) {
    sh command
}
