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
                         newVersion = sh(script: "npm version patch --no-git-tag-version --commit-hooks=false -m 'bump version to %s' | sed s/v//", returnStdout: true)
                         sh  "echo ${newVersion}"
                         newVersion = newVersion.trim()
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "git checkout -b release-${newVersion}"
                        sh "git commit -m 'bump version to ${newVersion}'"
                        sh "git push --no-verify --set-upstream origin release-${newVersion}"
                        sh "git push --tags --no-verify"
                    }
                }
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "git tag -a v${newVersion} -m 'bump version to ${newVersion}'"
                        sh "npm run release:notes"
                    }
                }
            }
        }
    }
}
