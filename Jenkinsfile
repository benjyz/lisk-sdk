/*
 * Copyright © 2018 Lisk Foundation
 *
 * See the LICENSE file at the top-level directory of this distribution
 * for licensing information.
 *
 * Unless otherwise agreed in a custom licensing agreement with the Lisk Foundation,
 * no part of this software, including this file, may be copied, modified,
 * propagated, or distributed except according to the terms contained in the
 * LICENSE file.
 *
 * Removal or modification of this copyright notice is prohibited.
 *
 */
@Library('lisk-jenkins') _
pipeline {
	agent { node { label 'lisk-elements' } }
	stages {
		stage('Install dependencies') {
			steps {
				sh 'npm install --verbose'
			}
		}
		stage('Build') {
			steps {
				sh 'npm run build'
			}
		}
		stage('Run lint') {
			steps {
				ansiColor('xterm') {
					sh 'npm run lint'
				}
			}
		}
		stage('Run tests') {
			steps {
				ansiColor('xterm') {
					sh 'npm run test'
				}
			}
		}
		stage('Run node tests') {
			steps {
				ansiColor('xterm') {
					sh 'npm run test:node'
				}
			}
		}
		stage('Run browser tests') {
			steps {
				sh '''
				npm run build:browsertest
				npm run test:browser
				'''
			}
		}
	}
	post {
		success {
			script {
				build_info = getBuildInfo()
				liskSlackSend('good', "Recovery: build ${build_info} was successful.")
			}
			deleteDir()
			githubNotify context: 'continuous-integration/jenkins/lisk-elements', description: 'The build passed.', status: 'SUCCESS'
		}
		failure {
			script {
				build_info = getBuildInfo()
				liskSlackSend('danger', "Build ${build_info} failed (<${env.BUILD_URL}/console|console>, <${env.BUILD_URL}/changes|changes>)\n")
			}
			archiveArtifacts allowEmptyArchive: true, artifacts: 'cypress/screenshots/'
			githubNotify context: 'continuous-integration/jenkins/lisk-elements', description: 'The build failed.', status: 'FAILURE'
		}
		aborted {
			githubNotify context: 'continuous-integration/jenkins/lisk-elements', description: 'The build was aborted.', status: 'ERROR'
		}
	}
}
