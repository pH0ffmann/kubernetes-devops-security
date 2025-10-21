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
          sh 'printenv'
          sh 'docker build -t pablohoffmann/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push pablohoffmann/numeric-app:""$GIT_COMMIT""'
        }

      }

    }
}