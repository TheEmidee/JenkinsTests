@Library('ue4-jenkins-library@2.9')
@Library('slack-notifier@master')
@Library('jenkins-utils@1.1')

import org.emidee.jenkins.DeploymentEnvironment
import org.emidee.jenkins.Environment

node('UE4') {
    initializeEnvironment( this, "Tests" )
    skipDefaultCheckout()

    ws( env.WORKSPACE ) {
        checkout scm

        def deployment_environment = Environment.instance.DEPLOYMENT_ENVIRONMENT as DeploymentEnvironment

        if ( deployment_environment == DeploymentEnvironment.Development ) {
            echo "Development"
            touch file: 'version.txt', timestamp: 0

            def git_branch = "develop"
            def origin_str = "origin/"

            if ( git_branch.startsWith( origin_str ) ) {
                git_branch = git_branch.substring( origin_str.length() )
            }

            bat "git switch -C ${git_branch} HEAD"
            bat "git config user.email jenkins@fishingcactus.com"
            bat "git config user.name Jenkins"
            bat "git add -A"
            bat "git commit -am \"Update version\" -n"

            sshagent( [ "JenkinsSwarms" ] ) {
                bat "git push --set-upstream origin ${env.GIT_BRANCH}"
            }
        } else {
            echo "NOT Development"
        }
    }
}
