pipeline {
    agent {
        label 'main'
    }

    environment {
        SONAR_ID     = credentials('sonar-login')
        ADMIN1C_USER = credentials('ADMIN1C_USER')
        PATH = "C:\\WINDOWS;C:\\WINDOWS\\SYSTEM32;C:\\Program Files\\AdoptOpenJDK\\jdk-11.0.8.10-hotspot\\bin;C:\\Program Files\\AdoptOpenJDK\\jdk-8.0.265.01-hotspot;C:\\opt\\OneScript\\bin;E:\\sonar-scanner\\bin;C:\\Program Files\\Git\\bin;"
    }

    stages {
        
        
    stage('Последний коммит'){
            steps {
                bat "git show --summary"
            }
    }
    
    stage("Syntax check") {
            steps {
                script{
                    try {
                        bat "vrunner syntax-check"
                    } catch(Exception Exc) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
    }  

    stage('Отключение конфигурации от хранилища') {
            steps {
                bat "runner unbindrepo --db-user=$ADMIN1C_USER --ibconnection /Slocalhost\\${JOB_NAME}"
            }
        }
    stage('Сборка') {
            steps {
                bat "runner init-dev --db-user=$ADMIN1C_USER --ibconnection /Slocalhost\\${JOB_NAME} --src=.\\src\\cf"
            }
        }

    stage("Sonar") {
            steps {
                bat "sonar-scanner -D sonar.login=$SONAR_ID -D sonar.projectVersion=1.0.${BUILD_ID} -D sonar.host.url=${SONAR_HOST} -D sonar.projectKey=${JOB_NAME} -D sonar.projectName=${JOB_NAME} -D sonar.sources=./src -D sonar.sourceEncoding=UTF-8 -D sonar.inclusions=src/**/*.bsl -D sonar.exclusions=**/\\u0440\\u0435\\u0433\\u043b\\u0430\\u043c\\u0435\\u043d\\u0442\\u0438\\u0440*/**/*.*
"
            }
        }
        
    stage("vanessa") {
            steps {
                script{
                    try {
                        bat "chcp 65001\n call runner vanessa"
                    } catch(Exception Exc) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
    }    

    stage("Allure report") {
            steps {
                allure includeProperties: false, jdk: '', results: [[path: 'out/syntax-check/allure'], [path: 'out/smoke/allure'], [path: 'out/allure']]
            }
        }    
    }   
}
