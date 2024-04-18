pipeline {
	options {
		timeout(time: 40, unit: 'MINUTES')
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}
	agent {
		label "centos-latest"
	}
	tools {
		maven 'apache-maven-latest'
		jdk 'temurin-jdk17-latest'
	}
	stages {
		stage('Build') {
			steps {
				wrap([$class: 'Xvnc', useXauthority: true]) {
					sh """
					mvn clean verify --batch-mode --fail-at-end -Dmaven.repo.local=$WORKSPACE/.m2/repository \
						-Pbuild-individual-bundles -Pbree-libs -Papi-check \
						-Dcompare-version-with-baselines.skip=false \
						-Dproject.build.sourceEncoding=UTF-8 
					"""
				}
			}
			post {
				always {
					archiveArtifacts artifacts: '*.log,*/target/work/data/.metadata/*.log,*/tests/target/work/data/.metadata/*.log,apiAnalyzer-workspace/.metadata/*.log', allowEmptyArchive: true
					publishIssues issues:[scanForIssues(tool: eclipse(pattern: '**/target/compilelogs/*.xml')), scanForIssues(tool: mavenConsole())]
				}
			}
		}
	}
}
