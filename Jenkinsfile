#!groovy

stage('Queue') {
    LABEL='Largest2'
    node((params.LABEL) ? params.LABEL : LABEL) {
//	checkout scm
//	jenkinsfile = load "${WORKSPACE}/kruize/Jenkinsfile"
//      jenkinsfile.testBuild()
	testBuild()
    }
}


def setupEnv() {
	JENKINS_FILE = params.JenkinsFile ? params.JenkinsFile : ""
	KRUIZE_REPO = params.KRUIZE_REPO ? params.KRUIZE_REPO : "https://github.com/kruize/kruize.git"
	KRUIZE_BRANCH = params.KRUIZE_BRANCH ? params.KRUIZE_BRANCH : "master"
	
	sh 'printenv'
}

def setup() {
	stage('Setup') {
		timestamps{
			setupEnv()

			if (fileExists('kruize')) {
				dir('kruize') {
					deleteDir()
				}
			}
			
			
			// kruize repository 
			if (params.KRUIZE_REPO) {
				env.KRUIZE_REPO = params.KRUIZE_REPO
			}

			if (params.KRUIZE_BRANCH) {
				env.KRUIZE_BRANCH = params.KRUIZE_BRANCH
			}		

			if (params.USER) {
				env.USER = params.USER
			}

			if (params.PASSWORD) {
				env.PASSWORD = params.PASSWORD
			}

			if (params.ICP_SERVER) {
				env.ICP_SERVER = params.ICP_SERVER
			}

			
			GIT_CMD = "git clone $KRUIZE_REPO"
		        echo "WORKSPACE = $WORKSPACE"

			sh "$GIT_CMD"
	
//			dir( WORKSPACE) {				
//				get_sources_with_authentication()
//			}
		}
	}
}

def get_sources_with_authentication() {
	sshagent(credentials:["${params.USER_CREDENTIALS_ID}"], ignoreMissing: true) {
		get_sources()
	}
}


def get_sources() {
	if (params.CUSTOMIZED_SDK_URL_CREDENTIAL_ID) {
		// USERNAME and PASSWORD reference with a withCredentials block will not be visible within job output
		withCredentials([usernamePassword(credentialsId: "${params.CUSTOMIZED_SDK_URL_CREDENTIAL_ID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
			sh "$GIT_CMD"
		}
	} else {
		sh "$GIT_CMD"
	}
}


def build() {
	stage('Build') {
		timestamps{
			echo 'Building Kruize...'			
			//BUILD_CMD = "$WORKSPACE/kruize/build.sh"
			sh "cd $WORKSPACE/kruize; ./build.sh"
		}
	}
}

def addJobDescription() {
	if (params.PERSONAL_BUILD) {
		// update build name if personal build
		wrap([$class: 'BuildUser']) {
			currentBuild.displayName = "#${BUILD_NUMBER} - ${BUILD_USER_EMAIL}"
		}
	}

	def description = (currentBuild.description) ? currentBuild.description + "<br>" : ""
	currentBuild.description = description \
		+ "TARGET: ${params.TARGET}<br/>" \
		+ "LABEL: <a href=${JENKINS_URL}label/${LABEL}>${LABEL}</a><br/>" \
		+ "<a href=${JENKINS_URL}computer/${NODE_NAME}>${NODE_NAME}</a>"
}


def deploy( ) {
	stage('Deploy') {
		timestamps{
			echo 'Deploying kruize...'
			sh "sed s/'cloudctl login -a'/'cloudctl login -u $USER -p $PASSWORD -n kube-system -a'/ ./kruize/deploy.sh > ./kruize/deploy_icp.sh"
       			sh "chmod 777 ./kruize/deploy_icp.sh"
        		sh "cd kruize; ./deploy_icp.sh -c icp -u https://$ICP_SERVER:8443"
		}
	}
}

def runTest( ) {
	stage('Test') {
		timestamps{
			echo 'Running tests...'
			
			TARGET = "${params.TARGET}";
			//RUN_CMD = "$WORKSPACE/../rt-cloud-benchmarks/$TARGET/tests/icp-load.sh "
			//sh "$RUN_CMD"
			sh "cd /var/lib/jenkins/workspace/rt-cloud-benchmarks/$TARGET; tests/icp-load.sh $ICP_SERVER"
		}
	}
}



def testBuild() {
	TIME_LIMIT =  params.TIME_LIMIT ? params.TIME_LIMIT.toInteger() : 10
	timeout(time: TIME_LIMIT, unit: 'HOURS') {
		try {
			addJobDescription()

			// prepare environment and compile test projects
			//if( params.IS_PARALLEL == true ){
			//	setupParallelEnv()
			//} else {

				echo "NODE_NAME = ${env.NODE_NAME}"
				setup()
				build()
				deploy()
				runTest()
				
			//	post("${env.BUILD_LIST}")
			//}
		} finally {
			if (!params.KEEP_WORKSPACE) {
				// cleanWs() does not work in some cases, so set opts below
//				cleanWs notFailBuild: true, disableDeferredWipeout: true, deleteDirs: true
			}
		}

	}
}




