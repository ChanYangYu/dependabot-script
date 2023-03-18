pipeline {
    agent {
        docker {
            image 'ruby:2.7.4-slim-buster'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'gem install bundler'
                sh 'bundle install'
            }
        }
        stage('Dependency Update') {
            steps {
                script {
                    docker.image('dependabot/dependabot-core:0.163.1').inside {
                        sh 'bundle update'
                    }
                }
            }
        }
        stage('Run') {
            withCredentials([string(credentialsId: 'root', variable: 'TOKEN')]) {
                sh """
                export DIRECTORY_PATH=/
                export BRANCH=$BRANCH
                export GITHUB_ACCESS_TOKEN=$TOKEN
                export PROJECT_PATH=$PROJECT_PATH
                export PACKAGE_MANAGER=npm_and_yarn

                bundle exec ruby ./generic-update-script.rb
                """
            }
        }
    }
}
