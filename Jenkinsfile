#!/usr/bin/env groovy

timestamps {

node ('slave') { 

	stage ('gol_mvn - Checkout') {
 	 checkout([$class: 'GitSCM', branches: [[name: '**']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '1401d42c-2f40-48d2-a5b3-c7e8029ed633', url: 'git@github.com:PavloPalamarchuksofts/game-of-life.git']]]) 
	}
	stage ('gol_mvn - Build') {
 	
withEnv(["JAVA_HOME=${ tool 'open-jdk-8' }", "PATH=${env.JAVA_HOME}/bin:$PATH"]) { 
		// Maven build step
	withMaven(jdk: 'open-jdk-8', maven: 'Maven-360') { 
 			if(isUnix()) {
 				sh "mvn clean install -DforkMode=never " 
			} else { 
 				bat "mvn clean install -DforkMode=never " 
			} 
 		}
		// JUnit Results
		junit '**/gameoflife-*/target/surefire-reports/*.xml' 
	}
	withSonarQubeEnv('sonarc') {
      // requires SonarQube Scanner for Maven 3.2+
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
    }
    googleStorageUpload([bucket: 'gs://gol-arts', credentialsId: 'jenkins-tasks-ss', pattern: 'gameoflife-web/target/gameoflife.war'])
}
}
node ('deployment') { 

	stage ('gol_deploy - Build') {
	    googleStorageDownload([bucketUri: 'gs://gol-arts/gameoflife-web/target/gameoflife.war', credentialsId: 'jenkins-tasks-ss', localDirectory: ''])
 	
        sh "cp gameoflife-web/target/gameoflife.war /opt/tomcat/webapps/"
	}
}
}
