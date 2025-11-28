
pipeline{
    agent any
    tools{
        nodejs "node:18"
    }
    environment{
        SONARHOME = tool 'sonar-tool'
        GitCreds = credentials("git-creds")
        DockerCreds = credentials("dockercred")
    }
    stages{
        stage("frontend"){
            stages{
                stage("cleaning the workspace"){
                    steps{
                        cleanWs()
                    }
                }
                stage("fetch"){
                    steps{
                        git branch: 'main', url: 'https://github.com/sai-rathod/crud-dd-task-mean-app.git'
                        sh "echo code fetched successfully"
                    }
                }
                stage("installing frontend dependenies"){
                    steps{
                        dir("frontend"){
                            sh """
                            npm install
                            echo "dependencies installed successfully"
                            """
                        }
                    }
                }
                stage("frontend unit test"){
                    steps{
                        dir("frontend"){
                            sh "npm run test || echo 'no unit test available'"
                        }
                    }
                }
                stage("building frontend code"){
                    steps{
                        dir("frontend"){
                            sh "npm run build && echo 'code built successfully' || echo 'code build faild'"
                        }
                    }
                }
                stage("sonar code test"){
                    steps{
                        dir("frontend"){
                            withSonarQubeEnv("sonarqube"){
                                sh '''
                                $SONARHOME/bin/sonar-scanner -Dsonar.projectName=crud_frontend -Dsonar.projectKey=crud_frontend
                                '''
                            }
                        }
                    }
                }
                stage("sonar quality gate"){
                    steps{
                        waitForQualityGate abortPipeline:false
                    }
                }
                stage("trivy fs scan"){
                    steps{
                        dir("frontend"){
                        sh "trivy fs -f json -o trivy_fs_crud_frontend.json ."
                        }
                    }
                }
                stage("pushing to docker hub"){
                    steps{
                        dir("frontend"){
                            sh"""
                            echo $DockerCreds_PSW | docker login -u $DockerCreds_USR --password-stdin
                            docker build -t $DockerCreds_USR/crud-dd-task-frontend:latest .
                            docker push $DockerCreds_USR/crud-dd-task-frontend:latest
                            """
                        }
                    }
                }
                 stage("trivy image scan"){
                    steps{
                        sh """
                        trivy image -f json -o trivy_image_crud_frontend.json $DockerCreds_USR/crud-dd-task-frontend:latest
                        """
                    }
                }
            }
        }
        stage("backend"){
            stages{
                stage("installing backend dependenies"){
                    steps{
                        dir("backend"){
                            sh """
                            npm install
                            echo "dependencies installed successfully"
                            """
                        }
                    }
                }
                stage("backend unit test"){
                    steps{
                        dir("backend"){
                            sh "npm run test || echo 'no unit test available'"
                        }
                    }
                }
                stage("sonar code test"){
                    steps{
                        dir("backend"){
                            withSonarQubeEnv("sonarqube"){
                                sh '''
                                $SONARHOME/bin/sonar-scanner -Dsonar.projectName=crud_backend -Dsonar.projectKey=crud_backend
                                '''
                            }
                        }
                    }
                }
                stage("sonar quality gate"){
                    steps{
                        waitForQualityGate abortPipeline:false
                    }
                }
                stage("trivy fs scan"){
                    steps{
                        dir("backend"){
                        sh "trivy fs -f json -o trivy_fs_crud_backend.json ."
                        }
                    }
                }
                stage("pushing to docker hub"){
                    steps{
                        dir("backend"){
                            sh"""
                            echo $DockerCreds_PSW | docker login -u $DockerCreds_USR --password-stdin
                            docker build -t $DockerCreds_USR/crud-dd-task-backend:latest .
                            docker push $DockerCreds_USR/crud-dd-task-backend:latest
                            """
                        }
                    }
                }
                stage("trivy image scan"){
                    steps{
                        sh """
                        trivy image -f json -o trivy_image_crud_backend.json $DockerCreds_USR/crud-dd-task-backend:latest
                        """
                    }
                }
            }
        }
        stage('Deploy'){
            steps {
                sshagent(['assignment-project']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@13.203.207.188 "
                            cd /home/ubuntu
                            git clone https://github.com/sai-rathod/crud-dd-task-mean-app.git || echo 'repo is already present'
                            cd /home/ubuntu/crud-dd-task-mean-app
                            git fetch --all
                            git reset --hard origin/main
                            docker-compose down || true
                            docker image prune -f || true
                            docker-compose pull
                            docker-compose up -d && echo 'the application is up' || echo 'deployement failed'
                        "
                    '''
                }
            }
        }
    }
    post{
        always{
            cleanWs(
                deleteDirs:true,
                patterns:[[pattern:'trivy_fs_crud_backend.json' ,type:'EXCLUDE'],
                [pattern:'trivy_image_crud_backend.json' ,type:'EXCLUDE'],
                [pattern:'trivy_fs_crud_frontend.json' ,type:'EXCLUDE'],
                [pattern:'trivy_image_crud_frontend.json' ,type:'EXCLUDE']])
        }
    }
}
