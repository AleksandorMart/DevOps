pipeline {
    agent any
    
    parameters {
        choice(
            name: 'ACTION',
            choices: ['deploy', 'start', 'stop'],
            description: 'Выберите действие'
        )
        string(
            name: 'TARGET_HOST',
            defaultValue: 'localhost',
            description: 'Целевой хост для развертывания'
        )
    }
    
    environment {
        ANSIBLE_CONFIG = 'ansible/ansible.cfg'
        PROJECTS_DIR = '/opt/microservices'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "Repository checked out successfully"
            }
        }
        
        stage('Prepare Environment') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                echo "Подготовка окружения на ${params.TARGET_HOST}"
                
                script {
                    ansiblePlaybook(
                        playbook: 'ansible/playbooks/site.yml',
                        inventory: 'ansible/inventory/hosts',
                        credentialsId: '',
                        extraVars: [
                            'docker_install': true,
                            'docker_compose_install': true,
                            'clone_repos': true,
                            'start_services': false,
                            'stop_services': false
                        ],
                        tags: 'infrastructure'
                    )
                }
                
                echo "Окружение подготовлено успешно"
            }
        }
        
        stage('Start Applications') {
            when {
                expression { params.ACTION == 'start' || params.ACTION == 'deploy' }
            }
            steps {
                echo "Запуск приложений на ${params.TARGET_HOST}"
                
                script {
                    ansiblePlaybook(
                        playbook: 'ansible/playbooks/site.yml',
                        inventory: 'ansible/inventory/hosts',
                        credentialsId: '',
                        extraVars: [
                            'docker_install': false,
                            'docker_compose_install': false,
                            'clone_repos': false,
                            'start_services': true,
                            'stop_services': false
                        ],
                        tags: 'start'
                    )
                }
                
                echo "Приложения запущены успешно"
            }
        }
        
        stage('Stop Applications') {
            when {
                expression { params.ACTION == 'stop' }
            }
            steps {
                echo "Остановка приложений на ${params.TARGET_HOST}"
                
                script {
                    ansiblePlaybook(
                        playbook: 'ansible/playbooks/site.yml',
                        inventory: 'ansible/inventory/hosts',
                        credentialsId: '',
                        extraVars: [
                            'docker_install': false,
                            'docker_compose_install': false,
                            'clone_repos': false,
                            'start_services': false,
                            'stop_services': true
                        ],
                        tags: 'stop'
                    )
                }
                
                echo "Приложения остановлены"
            }
        }
    }
    
    post {
        success {
            echo "Pipeline выполнен успешно!"
            emailext (
                subject: "SUCCESS: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER})",
                body: "Pipeline выполнен успешно. Детали: ${env.BUILD_URL}",
                to: 'user@example.com'
            )
        }
        failure {
            echo "Pipeline завершился с ошибкой"
            emailext (
                subject: "FAILURE: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER})",
                body: "Pipeline завершился с ошибкой. Детали: ${env.BUILD_URL}",
                to: 'user@example.com'
            )
        }
    }
}
