pipeline {
  agent {label "inbound-agent-jdk11"}
    tools {
        maven 'maven'
    }

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    } 

    stages {


        stage ('Initialize') {
            steps {
                withEnv(["PATH=${M2_HOME}/bin:$PATH"]) {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "building devsecops2 Version 1.0.${BUILD_NUMBER}"
                   # ${M2_HOME}/bin/mvn install
                '''
            }
            }
        }

        stage('Scan check') {
              steps {
                script{
	            def scannerHome = tool 'sonar-scanner';
                withSonarQubeEnv("sonarqube") { 
		          sh "${scannerHome}/bin/sonar-scanner --version"
                }
              }
            }
        }


        
        stage ('Build') {
            steps {
                sh 'mvn clean package install -DskipTests' 
            }
            post {
                success {
                    sh 'echo success'
                }
            }
        }
        
        stage('Build Images') {
            steps {

                // sh 'podman build  -t docker.idp.system.sumerge.local/ebc-mock-svc-test:0.1 .'
                
                sh 'echo skipping...'



		    
                // sh 'podman build --network host -t docker.idp.system.sumerge.local/devsecops2 ./ebc-mock-svc/'
                
                //sh 'echo Image build ebc-switch-connector'
                //sh 'docker build --network host -t sumergerepo/ebc-switch-connector:alpha ./ebc-switch-connector/'
                
                //sh 'echo Image build ipn-integration-connector'
                //sh 'docker build --network host -t sumergerepo/preauth-ipn-connector:alpha ./ipn-integration-platform-connector/'
            }
        }

	        stage('Push images') {
            steps {
                withCredentials([usernamePassword(credentialsId:"localrepo",usernameVariable:"USERNAME",passwordVariable:"PASSWORD")]) {
                    sh 'echo skipping ...'
                    // sh "podman login --tls-verify=false -u ${USERNAME} -p ${PASSWORD} docker.idp.system.sumerge.local"
                    // sh 'podman push docker.idp.system.sumerge.local/ebc-mock-svc-test:0.1 --tls-verify=false'



			
                    /* sh 'echo Push image preauthorized-integration-module'
                    // sh 'docker push sumergerepo/preauthorized-integration-module:beta'
                    // sh 'docker tag sumergerepo/preauthorized-integration-module:beta sumergerepo/preauthorized-integration-module:beta-1.0.${BUILD_NUMBER}'
                    // sh 'docker push sumergerepo/preauthorized-integration-module:beta-1.0.${BUILD_NUMBER}' */
                    
                    // sh 'echo Push image devsecops2'
                    /* sh 'docker tag sumergerepo/ebc-mock-svc:alpha sumergerepo/ebc-mock-svc:alpha-1.0.${BUILD_NUMBER}'
                    // sh 'docker push sumergerepo/ebc-mock-svc:alpha-1.0.${BUILD_NUMBER}' */
                    
                    // sh 'echo Push image ebc-switch-connector'
                    // sh 'docker push sumergerepo/ebc-switch-connector:alpha'
                    
                    // sh 'echo Push image preauth-ipn-connector'
                    // sh 'docker push sumergerepo/preauth-ipn-connector:alpha'
                }    
            }
        }        


        stage('Scan') {
            steps {
		    sh 'pwd && ls'
                script {
                def scannerHome = tool 'sonar-scanner';
                    withSonarQubeEnv("sonarqube") {
                    sh "${tool("sonar-scanner")}/bin/sonar-scanner -X \
                    -Dsonar.projectKey=security-check \
                    -Dsonar.login=sqa_d07109b7e09024f88859d77840ef87883affe482 \
                    -Dsonar.sources=./src \
                    -Dsonar.java.binaries=./target/classes \
		    -Dsonar.externalIssuesReportPaths=./sonar-deps-report.json \
                    -Dsonar.host.url=http://sonarqube.k8s.system.local "
                    
                  }
                }
            }
        }

        stage("TRIVY"){
            steps{
		    sh """cat <<EOF >>/etc/containers/registries.conf 
[[registry]] 
location = "docker.idp.system.sumerge.local" 
insecure = true  
"""
                sh " trivy image --timeout 900s --output=trivy-deps-report.json --format=json --insecure \
		    docker.idp.system.sumerge.local/ebc-mock-svc-test:0.1 "
		sh 'trivy sonarqube trivy-deps-report.json -- filePath=Dockerfile > sonar-deps-report.json' \
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

    }
}

  
  
  
//   stages {
//     // stage('git') {
//     //   steps {
//     //    git 'https://github.com/mostafa-abd-elrazik/devsecops-test.git'
//     //   }
//     // }
//     stage('trivy check') {
//       steps {
//         // sleep 360
//         sh """cat <<EOF >>/etc/containers/registries.conf 
// [[registry]] 
// location = "docker.idp.system.sumerge.local" 
// insecure = true  
// """
//         // sh 'cat /etc/containers/registries.conf'
//         sh 'podman build --tls-verify=false -t docker.idp.system.sumerge.local/dummy-image .'
//         // sh 'podman build --tls-verify=false -t docker.idp.system.sumerge.local/dummy-image .'
//         // sh 'buildah build --tls-verify=false -t docker.idp.system.sumerge.local/dummy-image:0.1 .'
//         sh 'trivy image --insecure  --timeout 7200s  --severity HIGH,CRITICAL http://docker.idp.system.sumerge.local/slave-trivy:latest'
//         // sh 'podman version'
//         // sh 'buildah images'
//         // sh 'buildah build --tls-verify=false -t docker.idp.system.sumerge.local/dummy-image .'
//         // sh 'trivy --timeout 900s image --severity HIGH,CRITICAL docker.idp.system.sumerge.local/dummy-image'
//       }
//     }

//   }
// }
