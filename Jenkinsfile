pipeline {
  agent none   // no default agent, we'll choose per stage
  triggers { githubPush() }   // auto-trigger from GitHub webhook
  options { timestamps() }

  stages {
    /* Stage 1: Run on Controller (built-in node) */
    stage('Controller Stage') {
      agent { label 'built-in' }
      steps {
        echo "Running on CONTROLLER"
        echo "NODE = ${env.NODE_NAME}"
        echo "WORKSPACE = ${env.WORKSPACE}"
      }
    }
    /* Stage 2: Run on new Windows Agent */
    stage('Windows Agent Stage') {
      agent { label 'win' }   // will match win-agent-2
      steps {
        bat '''
          echo Running on Windows Agent: %COMPUTERNAME%
          echo Current DIR:
          cd
          echo JAVA Version:
          java -version
        '''
      }
    }
    /* Stage 3: Run in Parallel (Controller vs Windows Agent) */
    stage('Parallel Test') {
      parallel {
        stage('Controller Lane') {
          agent { label 'built-in' }
          steps {
            echo "Hello from CONTROLLER in parallel"
          }
        }
        stage('Windows Lane') {
          agent { label 'win' }
          steps {
            bat "echo Hello from WINDOWS AGENT in parallel"
          }
        }
      }
    }
  }
  post {
    always {
      node('win') {   // ensure we’re on a Windows workspace
        archiveArtifacts artifacts: '**/*.log, artifact.txt', allowEmptyArchive: true
      }
    }
  }
}

