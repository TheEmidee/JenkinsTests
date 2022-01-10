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
            def GIT_COMMIT_MSG = sh (script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
            def GIT_AUTHOR = sh (script: 'git log -1 --pretty=%cn ${GIT_COMMIT}', returnStdout: true).trim()
            def GIT_AUTHOR_EMAIL = bat (
                script: "git --no-pager show -s --format=%%ae",
                  returnStdout: true
              ).split('\r\n')[2].trim()

            echo GIT_COMMIT_MSG
            echo GIT_AUTHOR
            echo GIT_AUTHOR_EMAIL

            echo "Development"

            //def IsManual = currentBuild.rawBuild.getCauses()[0].toString().contains('UserIdCause');
            echo "${currentBuild.buildCauses}" // same as currentBuild.getBuildCauses()
            echo "${currentBuild.getBuildCauses('hudson.model.Cause$UserCause')}"
            echo "${currentBuild.getBuildCauses('hudson.triggers.TimerTrigger$TimerTriggerCause')}"

            def IsJenkins = GIT_COMMIT_MSG.contains( "[Jenkins]" )

            echo IsJenkins

            if ( IsJenkins ) {
                currentBuild.result = "NOT_BUILT"
                return
            }

            writeFile file: 'version.txt', text: "$BUILD_NUMBER"

            def git_branch = "develop"
            def origin_str = "origin/"

            if ( git_branch.startsWith( origin_str ) ) {
                git_branch = git_branch.substring( origin_str.length() )
            }

            bat "git switch -C ${git_branch} HEAD"
            bat "git config user.email jenkins@fishingcactus.com"
            bat "git config user.name Jenkins"
            bat "git add -A"
            bat "git commit -am \"[Jenkins] Update version\" -n"

            sshagent( [ "JenkinsTestsDeployKey" ] ) {
                bat "git push --set-upstream origin develop"
            }
        } else {
            echo "NOT Development"
        }
    }
}
