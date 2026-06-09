pipeline{
  agent any

  stages{
    stage("Install maven"){
      steps{
        sh 'sudo apt update && sudo apt instal maven -y'
      }
    }
    stage("Generate java starter template"){
      steps{
       sh '''
         mvn archetype:generate \
         -DarchetypeGroupId=org.apache.maven.archetypes \
         - DarchetypeArtifactId=maven-archetype-quickstart \
         -DarchetypeVersion=1.5
         -DgroupId=com.simple.app
         -DartifactId=java-app
         -DinteractiveMode=false
       '''
       sh 'cd java-app'
      }
    }
    stage("Build artifact"){
      steps{
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage("Build docker image"){
      steps{
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
          ENTRYPOINT ['java', '-jar', 'app.jar']
        '''
        sh 'docker build -t java-app:local .'
      }
    }
    stage("Run docker container"){
      sh 'docker --rm --name java-app run java-app:local'
    }
  }
}
