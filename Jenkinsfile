#!/usr/bin/env groovy

pipeline {
    environment{
        REGISTRY=sh returnStdout: true, script: 'echo -n "${REGISTRY:=hub.krusche.cloud/demo/py}"'
        GIT_CODE=sh returnStdout: true, script: 'echo -n "${GIT_CODE=https://github.com/froggy777/py.git}"'
        IMAGE_TAG = "${RELEASE_NAME}-${BUILD_ID}"
        RELEASE_NAME=sh returnStdout: true, script: 'echo -n "${RELEASE_NAME=dev-py}"'
        REGISTRY_CRED=sh returnStdout: true, script: 'echo -n "${REGISTRY_CRED=ecr:eu-central-1:registry}"'
        REPO_CRED=sh returnStdout: true, script: 'echo -n "${REPO_CRED=repo-cred-id}"'
    }
    agent any
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    }
//    parameters {
//        gitParameter branchFilter: 'origin.*/(.*)', defaultValue: 'master', name: 'BRANCH_PY', type: 'PT_BRANCH', useRepository: env.GIT_CODE
//    }
    stages {
        stage('SCMs checkout') {
            parallel {
                stage('py') {
                    steps {
                          git branch: 'master', url: env.GIT_CODE
                    }
                }
            }
        }
        stage('BUILD') {
            parallel {
                stage('build and push py') {
                    steps {
                        script {
                            sh "env"
                            docker.withRegistry('https://' + env.REGISTRY) {
                                def DockerImagePy = docker.build("${REGISTRY}:${IMAGE_TAG}", "-f Dockerfile .")
                                DockerImagePy.push()
                            }
                        }
                    }
                }
            }
        }
    }
}
