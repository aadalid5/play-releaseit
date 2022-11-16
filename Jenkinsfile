pipeline {
    agent any
    
    tools { nodejs "node" }
    
    environment {
        FOO = "initial FOO value"
    }

    stages {
        stage('git remote'){
            steps {
                sshagent(["github-key-a-id"]){
                    script {
                        sh 'git remote set-url origin  git@github.com:aadalid5/play-releaseit.git'
                        // sh 'git push origin HEAD:main && git push --tags'
                    }
            }
            }
        }

        // stage("checkout from version control"){
        //     steps{
        //         withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) {
        //             sh "git fetch"
        //             sh "git checkout main"
        //             sh "git reset --hard HEAD"
        //          }
        //     }
        // }

        stage("install"){
            steps{
                sh "npm i"
            }
        }

        // stage("Bump Package Version") {
        //     steps {
        //         withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
        //             script {
        //                  newVersion = sh(script: "npm version patch --commit-hooks=false -m 'bump version to %s' | sed s/v//", returnStdout: true)
        //                  newVersion = newVersion.trim()
        //             }
        //         }
        //     }
        // }

        // stage("Deploy") {
        //     steps {
        //         withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
        //             script {
        //                 sh "git checkout -b release-${newVersion}"
        //                 sh "git push --no-verify --set-upstream origin release-${newVersion}"
        //                 sh "git push --tags --no-verify"
        //             }
        //         }
        //         withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
        //             script {
        //                 sh "npm run release"
        //             }
        //         }
        //     }
        // }

        stage('post release-bump version'){
            steps{
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "git fetch"
                        sh "git checkout main"
                        // sh "git pull"
                        sh "git reset --hard HEAD"

                        newVersion = sh(script: "npm version patch --commit-hooks=false -m 'bump version to %s' | sed s/v//", returnStdout: true)
                        newVersion = newVersion.trim()

                        sh "git push --no-verify && git push --tags --no-verify"
                        // sh "git checkout -b release-${newVersion}"
                        // sh "git push --no-verify --set-upstream origin release-${newVersion}"
                        // sh "git push --tags --no-verify"
                    }
                    
                    sh "npm run release"
                }
            }
        }
    }
}
