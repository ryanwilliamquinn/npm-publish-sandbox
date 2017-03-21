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
        def userInput = input(
            id: 'semverTypeInput',
            message: 'Shall we publish?',
            ok: 'Yes go do it',
            parameters: [choice(choices: "patch\nminor\nmajor", description: 'semver types', name: 'semverType')],
            submitterParameter: 'versionType')

        stage('Deploy') {
            inBuildAgent 'npm version ${userInput['semverType']]}; npm publish'
            git push origin master
            git push origin --tags
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
