node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
	def server = Artifactory.server "jfrog"
	// Create an Artifactory Maven instance.
	def rtMaven = Artifactory.newMavenBuild()
	def buildInfo
       stage('Configure') {
        env.PATH = "${tool 'maven3'}/bin:${env.PATH}"
        version = '1.0.' + env.BUILD_NUMBER
        currentBuild.displayName = version

        properties([
                buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')),
                [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/jijeesh/spring-boot-sample/'],
                pipelineTriggers([[$class: 'GitHubPushTrigger']])
            ])
    }
    stage('Artifactory configuration') {
		// Tool name from Jenkins configuration
		rtMaven.tool = "maven3"
		// Set Artifactory repositories for dependencies resolution and artifacts deployment.
		rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
		rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
	}
 
// This displays colors using the 'xterm' ansi color map.
    ansiColor('xterm') {
        // Just some echoes to show the ANSI color.
        stage "\u001B[31mI'm Red\u001B[0m Now not"
    }
    
    stage('Checkout') {
        git 'https://github.com/jijeesh/spring-boot-sample'
    }

    stage('Version') {
        sh "echo \'\ninfo.build.version=\'$version >> src/main/resources/application.properties || true"
        sh "mvn -B -V -U -e versions:set -DnewVersion=$version"
    }

    stage('Build') {
        sh 'mvn -B -V -U -e clean package'
    }
    stage('Maven build') {
		buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
	}

	stage('Publish build info') {
		server.publishBuildInfo buildInfo
	}

    stage('Archive') {
        junit allowEmptyResults: true, testResults: '**/target/**/TEST*.xml'
    }

    stage('Deploy') {
        // Depends on the 'Credentials Binding Plugin'
        // (https://wiki.jenkins-ci.org/display/JENKINS/Credentials+Binding+Plugin)
        withCredentials([[$class          : 'UsernamePasswordMultiBinding', credentialsId: 'cloudfoundry',
                          usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
            sh '''
                curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar -zx

                ./cf api https://api.run.pivotal.io
                ./cf auth $USERNAME $PASSWORD
                ./cf target -o bertjan-demo -s development
                ./cf push
               '''
        }
    }
}
