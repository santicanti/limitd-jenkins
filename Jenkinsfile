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
        echo 'Creating bundle...'
        sh 'npm run create-bundle -- VERSION_NUMBER=' + currentBuild.number + ' WORKSPACE=..'
    }
}
