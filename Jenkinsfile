node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
	// def server = Artifactory.server "jfrog"
	// // Create an Artifactory Maven instance.
	// def rtMaven = Artifactory.newMavenBuild()
	// def buildInfo
  //      stage('Configure') {
  //       env.PATH = "${tool 'maven3'}/bin:${env.PATH}"
  //       version = '1.0.' + env.BUILD_NUMBER
  //       currentBuild.displayName = version
	//
  //       properties([
  //               buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')),
  //               [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/jijeesh/spring-boot-sample/'],
  //               pipelineTriggers([[$class: 'GitHubPushTrigger']])
  //           ])
  //   }
  //   stage('Artifactory configuration') {
	// 	// Tool name from Jenkins configuration
	// 	rtMaven.tool = "maven3"
	// 	// Set Artifactory repositories for dependencies resolution and artifacts deployment.
	// 	rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
	// 	rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
	// }
	def mvnHome = tool 'maven3'
 def jdkHome = tool 'jdk8'

	stage('Configure') {
      //  env.PATH = "${tool 'maven3'}/bin:${env.PATH}"
			//	env.PATH = "${tool 'jdk8'}/bin:${env.PATH}"

}

stage ('Check Versions'){
	if (isUnix()) {

		sh "'${mvnHome}/bin/mvn'-version"
		sh "'${jdkHome}/bin/java' version"
	}else {
		echo "======================"
		echo "${mvnHome}"
		bat "'${mvnHome}/bin/mvn' -version"
		bat "'${jdkHome}/bin/java' version"
	}
}

    stage('Checkout') {
        git 'https://github.com/kantamn/spring-boot-sample'
    }

    stage('Version') {
				if (isUnix()) {
	        sh "echo \'\ninfo.build.version=\'$version >> src/main/resources/application.properties || true"
	        sh "mvn -B -V -U -e versions:set -DnewVersion=$version"
				}else{
					bat "echo \'\ninfo.build.version=\'$version >> src/main/resources/application.properties || true"
				 bat "mvn -B -V -U -e versions:set -DnewVersion=$version"
				}
    }

    stage('Build') {
			if (isUnix) {
				sh 'mvn -B -V -U -e clean package'
			}else{
				bat 'mvn -B -V -U -e clean package'
			}

    }
    stage('Maven build') {
		//buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
	}

	stage('Publish build info') {
		server.publishBuildInfo buildInfo
	}

    stage('Archive') {
        junit allowEmptyResults: true, testResults: '**/target/**/TEST*.xml'
    }

    // stage('Deploy') {
    //     // Depends on the 'Credentials Binding Plugin'
    //     // (https://wiki.jenkins-ci.org/display/JENKINS/Credentials+Binding+Plugin)
    //     withCredentials([[$class          : 'UsernamePasswordMultiBinding', credentialsId: 'cloudfoundry',
    //                       usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
    //         sh '''
    //             curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar -zx
		//
    //             ./cf api https://api.run.pivotal.io
    //             ./cf auth $USERNAME $PASSWORD
    //             ./cf target -o bertjan-demo -s development
    //             ./cf push
    //            '''
    //     }
    // }
}
