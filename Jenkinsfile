pipeline {
    agent any
    
    tools { nodejs "node" }
    
    environment {
        FOO = "initial FOO value"
    }

    stages {
        stage("install"){
            steps{
                sh "npm i"
            }
        }
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

        stage('post release-bump version'){
            steps{
                sshagent(["github-key-a-id"]){
                    script {
                        sh "git fetch"
                        sh "git checkout main"
                        sh "git pull"
                        sh "git reset --hard HEAD"
                        newVersion = sh(script: "npm version patch --commit-hooks=false -m 'bump version to %s'", returnStdout: true)
                        sh "git push --no-verify && git push --tags --no-verify"
                    }
                }
                withCredentials([gitUsernamePassword(credentialsId: 'git-hbrjenkins')]) { 
                    script {
                        sh "npx release-it@14.14.3 --no-npm --no-git --github.no-increment --github.release --ci"
                    }
                }
            }
        }
    }
}
