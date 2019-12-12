node('smartd-jnlp') {
    stage('Clone') {
        echo "1 clone stage..."
        git url: "https://github.com/sdxy36/jenkins-demo.git"
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }

    stage('Test') {
        echo "2 test stage..."
    }

    stage('Build') {
        echo "3 build stage..."
        withDockerRegistry(credentialsId: 'biao', url: 'https://index.docker.io/v1/') {
            def image = docker.build("sdxy36/jenkins-demo:${build_tag}")
            image.push()
        }
    }

    stage('Deploy') {
        echo "4 deploy stage..."
        if (env.BRANCH_NAME == 'master') {
            input "确认要部署线上环境吗？"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/g' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"

        echo "This is a deploy step to ${userInput} env"
        sh "kubectl apply -f k8s.yaml --record"
    }
 
}
