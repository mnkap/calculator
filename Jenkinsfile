pipeline {
  agent {
    docker { image 'node:16-alpine' }
  }


triggers {
 pollSCM('* * * * *')
}
 	stages {
 		stage("Compile") {
 			steps {
 				sh "./gradlew compileJava"
 		}	
 	}	
 		stage("Unit test") {
 			steps {
 				sh "./gradlew test"
 		}	
 	}	
 	
stage("Code coverage") {
 steps {
 sh "./gradlew jacocoTestReport"
 sh "./gradlew jacocoTestCoverageVerification"
 }
}

stage("Static code analysis") {
 steps {
 sh "./gradlew checkstyleMain"
 }
}	
stage("Package") {
 steps {
 sh "./gradlew build"
 }
}

stage("Docker run") {
sh "docker run -t -v /var/run/docker.sock:/var/run/docker.sock gbt1/docker_agent"
}
stage("Docker build") {
 steps {
 sh "docker build -t gbt1/calculator ."
 }
}

stage("Deploy to staging") {
 steps {
 sh "docker run -d --rm -p 8765:8080 --name calculator gbt1/calculator"
 }
}

stage("Acceptance test") {
 steps {
 sleep 60
 sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
 }
}

	}

post {
 always {
 sh "docker stop calculator"
 }
}
}
