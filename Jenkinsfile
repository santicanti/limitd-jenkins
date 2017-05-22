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
        sh 'npm i'
    }

    stage('Test') {
        echo 'Testing...'
        sh 'npm test'
    }

    stage('Create bundle') {
        sh 'rm *.deb'
        echo 'Creating bundle...'
        sh 'npm run create-bundle -- VERSION_NUMBER=1.0.' + currentBuild.number + ' WORKSPACE=..'
    }

    stage('Call packer job') {
        echo 'Calling packer job...'
        def files = findFiles(glob: '*.deb')

        PACKAGEPATH = sh(
          script: 'pwd',
          returnStdout:true
        ).trim() + '/' + files[0].name

        echo "Path to package created: ${PACKAGEPATH}"

        build job: 'create-ami', parameters: [[$class: 'StringParameterValue', name: 'PACKAGE_PATH', value: PACKAGEPATH]]
    }
}
