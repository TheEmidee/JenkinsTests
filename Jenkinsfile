@Library('ue4-jenkins-library@2.9')
@Library('slack-notifier@master')
@Library('jenkins-utils@1.1')

import org.emidee.jenkins.DeploymentEnvironment
import org.emidee.jenkins.Environment

node('UE4') {
    initializeEnvironment( this, "Tests" )

    def deployment_environment = Environment.instance.DEPLOYMENT_ENVIRONMENT as DeploymentEnvironment

    if ( deployment_environment == DeploymentEnvironment.Development ) {
        echo "Development"
    } else {
        echo "NOT Development"
    }
}