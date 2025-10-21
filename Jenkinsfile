pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        }
      stage('Unit Tests - JUnit and jacoco') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        }

      stage('Docker Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
            sh 'printenv'
            sh 'docker build -t pablohoffmann/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push pablohoffmann/numeric-app:""$GIT_COMMIT""'
          }
        }

      }

       stage('K8S Deployment - DEV') {
        steps {
          parallel(
            "Deployment": {
              withKubeConfig([credentialsId: 'kubeconfig']) {
                sh "bash k8s-deployment.sh"
              }
            },
            //"Rollout Status": {
            //  withKubeConfig([credentialsId: 'kubeconfig']) {
            //    sh "bash k8s-deployment-rollout-status.sh"
            //}
            }
          )
        }
    }

    }
}