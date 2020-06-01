def COLOR_MAP = ['SUCCESS': 'good', 'FAILURE': 'danger', 'UNSTABLE': 'danger', 'ABORTED': 'danger']
//def icons = ['SUCCESS' : 'man_dancing' , 'FAILURE' : 'ghost']
def icons = [":unicorn_face:",  ":man_dancing:", ":party_parrot:",  ":dancer:"]
def icons1 = [":Thermometer:", ":Bomb:"]
def randomIndex = (new Random()).nextInt(icons.size())
def randomIndex1 = (new Random()).nextInt(icons1.size())
pipeline {
   agent any
    options { skipDefaultCheckout() }
	environment { 
        branch = 'master' 
        credentialsId = 'dd'     // credential of jenkins 
        scmUrl = 'https://github.com/Dileep1314/new-gradle.git'
    } 
    stages {
        stage ('Build Stage') {
            agent none
            steps {
                script {
                    stage ('Checkout') {
                        echo 'THIS IS FOR CLONEING'
                        git branch: branch, credentialsId: credentialsId, url: scmUrl
                    }
                    stage ('BUILD') {
					    sh 'gradle task '
                        
                    }
				}
			}
		}
    }
 def notifyGIT(String state) {
 
    if('SUCCESS' == state || 'FAILED' == state) {
    // Set result of currentBuild !Important!
        currentBuild.result = state
    }
 
    notifyGit commitSha1: this.COMMIT_HASH, considerUnstableAsSuccess:        true, credentialsId: 'dileep', disableInprogressNotification: false, ignoreUnverifiedSSLPeer: true, includeBuildNumberInKey: false, prependParentProjectKey: false, projectKey: '', stashServerBaseUrl: 'https://github.com/Dileep1314/new-gradle.git'
 
}

}
