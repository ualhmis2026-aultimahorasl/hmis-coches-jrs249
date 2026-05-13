pipeline {
  agent any
  tools {
    maven "Maven-3"
  }
  stages {

    stage('Git fetch') {
      steps {
        git credentialsId: 'a44bbb84-bd1e-48fb-b1a4-80d3c634f998',
            branch: 'main',
            url: 'https://github.com/ualeda1-2026/RamirezSanchezJorgeEDA1-2026.git'
      }
    }

    stage('Compile, Test, Package') {
      steps {
        sh "mvn -f pom.xml clean package"
      }
      post {
        success {
          junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml'
          archiveArtifacts '**/target/*.jar'
          jacoco(
            execPattern: '**/target/jacoco.exec',
            classPattern: '**/target/classes',
            sourcePattern: '**/src/',
            exclusionPattern: '**/test/'
          )
        }
      }
    }

    stage('Analysis') {
      steps {
        sh "mvn -f pom.xml checkstyle:checkstyle pmd:pmd pmd:cpd com.github.spotbugs:spotbugs-maven-plugin:spotbugs"
      }
    }

    stage('Dependency-Check') {
      steps {
        sh "mvn -f pom.xml org.owasp:dependency-check-maven:aggregate"
      }
    }

    stage('Documentation') {
      steps {
        sh "mvn -f pom.xml javadoc:javadoc javadoc:aggregate"
      }
      post {
        success {
          step $class: 'JavadocArchiver', javadocDir: 'target/reports/apidocs', keepAll: false
        }
      }
    }

  }
}
