echo "Branch name: ${BRANCH_NAME}"
BUILD = BRANCH_NAME == 'master' ? 'latest' : BRANCH_NAME
echo "Build: ${BUILD}"

node ('master') {
    checkout scm
    echo "Build: ${BUILD}"
    stage ('Build Building container') {
        echo "Build: ${BUILD}"
        sh "sudo docker build -t devops/build-spring-boot-dummy:${BUILD} ."
    }
    stage ('Build in Container') {
        def CONTAINER_ID = sh (
                script: "sudo docker run -d --network=host devops/build-spring-boot-dummy:${BUILD}",
                returnStdout: true
        ).trim()
        echo "Container id: ${CONTAINER_ID}"

        sh "sudo docker exec ${CONTAINER_ID} ./gradlew build"
        sh "sudo docker cp ${CONTAINER_ID}:/spring-boot-dummy/build/libs/spring-boot-dummy-0.1.0.jar docker/spring-boot-dummy-0.1.0.jar"
        sh "sudo docker stop ${CONTAINER_ID}"
        sh "sudo docker rm ${CONTAINER_ID}"
    }

    stage ('Build Container with latest Build') {
        dir('docker') {
            sh "sudo docker build -t devops/spring-boot-dummy:${BUILD} ."
            sh "sudo docker push devops/spring-boot-dummy:${BUILD}"
        }
    }
}
