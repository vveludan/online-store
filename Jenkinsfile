#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    docker.image('jhipster/jhipster:v6.1.0').inside('-u jhipster -e GRADLE_USER_HOME=.gradle') {
        stage('check java') {
            sh "java -version"
        }

        stage('clean') {
            sh "chmod +x gradlew"
            sh "./gradlew clean --no-daemon"
        }

        stage('npm install') {
            sh "./gradlew npm_install -PnodeInstall --no-daemon"
        }

        stage('backend tests') {
            try {
                sh "./gradlew test integrationTest -PnodeInstall --no-daemon"
            } catch(err) {
                throw err
            } finally {
                junit '**/build/**/TEST-*.xml'
            }
        }

        stage('frontend tests') {
            try {
                sh "./gradlew npm_run_test -PnodeInstall --no-daemon"
            } catch(err) {
                throw err
            } finally {
                junit '**/build/test-results/TESTS-*.xml'
            }
        }

        stage('packaging') {
            sh "./gradlew bootJar -x test -Pprod -PnodeInstall --no-daemon"
            archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
        }

    }
}
