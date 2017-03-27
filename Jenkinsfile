properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])

node {
    try {
        stage("Checkout") {
            checkout scm
        }
        stage("Install") {
            sh 'npm install'
        }
        stage("Lint") {
            sh 'npm run lint -s'
        }
        stage("Tag build info") {
            sh "git rev-parse --short HEAD > git-commit-id"
            commit_id = readFile('git-commit-id').trim()
            writeFile file: 'build-id', text: env.BUILD_NUMBER
        }
        stage("Build internal") {
            withEnv(["INTERNAL=1"]) {
                sh 'npm run build -s'
                sh 'mv dist dist_internal'
            }
        }
        stage("Archive internal") {
            archiveArtifacts artifacts: "git-commit-id, build-id, dist_internal/**/*", fingerprint: false
        }
        // stage("Build external") {
        //     sh 'npm run build -s'
        // }
        // stage("Archive external") {
        //     archiveArtifacts artifacts: "git-commit-id, build-id, dist/**/*", fingerprint: false
        // }
        currentBuild.result = "SUCCESS"
    } catch(e) {
        currentBuild.result = "FAILURE"
        throw e
    }
}
