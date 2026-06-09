pipeline{
  agent any

  stages{
    // stage("Install maven"){
    //   steps{
    //     sh 'apt update && sudo apt instal maven -y'
    //   }
    // }
    stage("Generate java starter template"){
      steps{
       sh '''
         mvn archetype:generate \
         -DarchetypeGroupId=org.apache.maven.archetypes \
         -DarchetypeArtifactId=maven-archetype-quickstart \
         -DarchetypeVersion=1.5 \
         -DgroupId=com.simple.app \
         -DartifactId=java-app \
         -DinteractiveMode=false
         pwd
       '''
      }
    }
    stage("Build artifact"){
      steps{
        sh 'pwd && cd java-app && mvn -B -DskipTests clean package'
      }
    }
    stage("Build docker image"){
      steps{
        dir('java-app'){
           sh '''
            cat > .dockerignore << 'EOF'
            .git*
            target/*
            !target/java-app-1.0-SNAPSHOT.jar
            EOF
          '''
          sh '''
            cat > Dockerfile << 'EOF'
            FROM eclipse-temurin:17-jre
            WORKDIR /app
            COPY target/java-app-1.0-SNAPSHOT.jar app.jar
            ENTRYPOINT ["java", "-cp", "app.jar", "com.simple.app.App"]
          '''
          sh 'docker build -t java-app:local .' 
        }
      }
    }
    stage("Run docker container"){
      steps{
        sh 'docker run --rm --name java-app java-app:local'
      }
    }
  }
}
