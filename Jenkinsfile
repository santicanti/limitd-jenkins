node {
    stage('Initialize') {
        echo 'Initializing...'
        def node = tool name: 'node-v4.4.5', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
        env.PATH = "${node}/bin:${env.PATH}"
    }

    stage('Checkout') {
        echo 'Getting source code...'
        checkout scm
    }

    stage('Install dependencies') {
        echo 'Installing dependencies...'
        try {
          sh 'npm i'
        } catch (err) {
          error("There was an error installing the dependencies: %{err}")
        }
    }

    stage('Test') {
        echo 'Testing...'
        try {
          sh 'npm test'
        } catch (err) {
          error("There was an error in the tests see above for more details")
        }
    }

    stage('Create package') {
        echo 'Deleting old packages and creating new one...'
        sh 'rm -f *.deb'
        try {
          sh 'npm run create-package -- VERSION_NUMBER=1.0.' + currentBuild.number + ' WORKSPACE=.'
        } catch (err) {
          error("There was an error creating the package: ${err}")
        }
    }

    stage('Call packer job') {
        echo 'Calling packer job...'
        try {
          def files = findFiles(glob: '*.deb')

          PACKAGEPATH = sh(
          script: 'pwd',
          returnStdout:true
          ).trim() + '/' + files[0].name
        } catch (err) {
          echo "There was an error preparing the package path: ${err}"
          throw new Exception()
        }

        echo "Path to package created: ${PACKAGEPATH}"

        try {
          build job: 'create-ami-pipeline', parameters: [[$class: 'StringParameterValue', name: 'PACKAGE_PATH', value: PACKAGEPATH]]
        } catch (err) {
          error("There was an error in the AMI creation job: ${err}")
        }
    }
}
