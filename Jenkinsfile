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
        sh 'npm run create-bundle -- VERSION_NUMBER=1.0.' + currentBuild.number + ' WORKSPACE=..'
    }

    stage('Call packer job') {
        echo 'Calling packer job...'
        //def packageDir = findFiles(glob: '**/*.deb')
        //echo """${packageDir[0].directory}"""
        def files = findFiles(glob: '*.deb')
        //echo pwd + """/${files[0].path}        ${files[0].name}"""
        //sh 'rm *.deb'
        //sh 'pwd'

        def newestFile = 0

        for (i=1; i < files.length; i++) {
          if (files[i].lastModified > files[newestFile].lastModified) {
            newestFile = i
          }
        }

        PACKAGEDIR = sh(
          script: 'pwd',
          returnStdout:true
        ).trim() + '/' + files[newestFile].name

        echo "${PACKAGEDIR}"

        PACKAGEDIRECTORY = sh(
          script: 'pwd',
          returnStdout:true
        ).trim()

        build job: 'create-ami', parameters: [[$class: 'StringParameterValue', name: 'PACKAGE_DIR', value: PACKAGEDIRECTORY]]
    }
}
