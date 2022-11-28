pipeline {
    agent any
    
    tools { nodejs "node" }
    
    environment {
        FOO = "initial FOO value"
    }

    stages {
        stage('git set remote test'){
            steps {
                sshagent(["github-key-a-id"]){
                    script {
                        sh 'git remote set-url origin  git@github.com:aadalid5/play-releaseit.git'
                        // sh 'git push origin HEAD:main && git push --tags'
                    }
            }
            }
        }

        stage("Checkout from Version Control"){
            steps{
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) {
                    sh "git fetch"
                    sh "git checkout main"
                    sh "git pull"
                    sh "git reset --hard HEAD"
                 }
            }
        }

        stage("Install"){
            steps{
                sh "npm i"
            }
        }

        stage("Bump Package Version") {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                         newVersion = sh(script: "npm version patch --no-git-tag-version --commit-hooks=false | sed s/v//", returnStdout: true)
                         newVersion = newVersion.trim()
                         versionMessage = "bump version to ${newVersion}"
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "git checkout -b release-${newVersion}"
                        sh "git commit -am '${versionMessage}'"
                        sh "git push --no-verify --set-upstream origin release-${newVersion}"
                    }
                }
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "git tag -a v${newVersion} -m '${versionMessage}'"
                        sh "git push --tags --no-verify"
                        sh "npx release-it@15.5.0 --no-npm --no-git --no-increment --github.release --github.update --ci"
                    }
                }
            }
        }
    }
}
