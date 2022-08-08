node {
        def majorVersion="1.0.${BUILD_NUMBER}"
	currentBuild.displayName = majorVersion

	//currentBuild.displayName = "2.0.${BUILD_NUMBER}"
	def GIT_COMMIT
  stage ('cloning the repository'){
	  
      def scm = git 'https://github.com/ravishankarojha/Jpetstore-parker-4'
	  GIT_COMMIT = sh(returnStdout: true, script: "git rev-parse HEAD").trim()
	  echo "COMMITID ${GIT_COMMIT}"
	  //echo "BBBB ${scm}"
	  //GIT_COMMIT = scm.GIT_COMMIT
	   //echo "**** ${GIT_COMMIT}"
	  
  }
	
 
  stage ('Build') {
      withMaven(jdk: 'java1.8', maven: 'Maven3.8.2') {
      sh 'mvn clean package'
	      echo "**** ${GIT_COMMIT}"
	//step($class: 'UploadBuild', tenantId: "5ade13625558f2c6688d15ce", revision: "${GIT_COMMIT}", appName: "JPetStore", requestor: "admin", id: "${newComponentVersionId}" )
	
	     
    }
  }
  
  stage ('Junit Testcase'){
  withMaven(jdk: 'java1.8', maven: 'Maven3.8.2') {
      sh 'mvn test -Dtest=Runner'	     
    }
  }

	echo("************************** Test Result Upload Started to Velocity****************************")
                        try{
                        step([$class: 'UploadJUnitTestResult',
                            properties: [
                        // Need to change the path of the test result xml result required.               
                                filePath: "target/surefire-reports/TEST-org.mybatis.jpetstore.service.OrderServiceTest.xml",
                                tenant_id: "5ade13625558f2c6688d15ce",
                                appName: "JPetStore-Distributed",
                                //appExtId: "4b006cdb-0e50-43f2-ac87-a7586a65389e",
				appExtId: "e6bb5903-f71b-4d3a-b0da-c7b5ccc2dd87",
				//appId: "acdfae67-616f-43e5-8872-2cfa3aa583de",    
                                name: "Junit Test - 1.0.${BUILD_NUMBER}",
                                testSetName: "Junit Test Run from Jenkins"]
                           
                        ])}catch(e){
                        throw e
                        }
                       
         echo("************************** Test Result Uploaded Successful to Velocity****************************")
	
	stage('SonarQube Analysis'){
		def mvnHome = tool name : 'Maven3.8.2', type:'maven'
		//def path = tool name: 'gradle-4.7', type: 'gradle'
		
		withSonarQubeEnv('sonar-server'){
			 //"SONAR_USER_HOME=/opt/bitnami/jenkins/.sonar ${mvnHome}/bin/mvn sonar:sonar"
			//SONAR_MAVEN_GOAL -Dsonar.host.url="http://ec2-3-130-60-241.us-east-2.compute.amazonaws.com:50000
		//	 sh  "mvn sonar:sonar -Dsonar.projectName=JPetStore -Dsonar.host.url=http://10.83.68.20:9000 sonarqube"
		//	mvn sonar:sonar \
                      //  -Dsonar.login=ad0309b56192390d914725cb9ded71f04e5a902c
		        //   sh  "mvn sonar:sonar -Dsonar.projectName=JPetStore-Distributed" 
		////	  sh  "sonar:sonar -Dsonar.projectName=JpetStore-Accelerate -Dsonar.host.url=http://localhost:50000 sonarqube"
			//sh "${path}/bin/gradle --info -Dsonar.host.url=http://localhost:9000 sonarqube"
			
		     sh	"mvn sonar:sonar -Dsonar.projectKey=JPetStore -Dsonar.host.url=http://10.83.68.20:9000 -Dsonar.login=ad0309b56192390d914725cb9ded71f04e5a902c"
		}
	 }
	
	
//stage ("Appscan"){
  //   //  appscan application: '84963f4f-0cf4-4262-9afe-3bd7c0ec3942', credentials: 'Credential for ASOC', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 20)], name: '84963f4f-0cf4-4262-9afe-3bd7c0ec39421562', scanner: static_analyzer(hasOptions: false, target: 'D:/Installables/Jenkins/workspace/Velocity/AltoroJ/build/libs/'), type: 'Static Analyzer'
  //	build job: '/velocity/Jpetstore/asoc', wait: false, parameters: [
	////build job: '/asoc', wait: false, parameters: [
	////string(name: 'COMMITID', value: GIT_COMMIT),
	//string(name: 'COMMITID', value: GIT_COMMIT),
	//string(name: 'parentBuildNumber', value: BUILD_NUMBER)
	////string(name: 'parentBuildNumber', value: 2.0.${BUILD_NUMBER})
		
	//]
//}
	
echo "(*******)"
stage('Publish Artificats to HCL Launch'){	
  step([$class: 'UCDeployPublisher',
	        siteName: 'Launch_Local',
	        component: [
	            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	            componentName: 'WebComponent',
	            createComponent: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                componentTemplate: '',
	                componentApplication: 'JPetStore-Distributed'
	            ],
	            delivery: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                pushVersion: '1.1.${BUILD_NUMBER}',
	                //baseDir: '/var/jenkins_home/workspace/JPetStore/target',
			 baseDir: '/var/lib/jenkins/workspace/JPetStore/target/',
	                fileIncludePatterns: '*.war',
	                fileExcludePatterns: '',
	               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
	                pushDescription: 'Pushed from Jenkins'
	            ]
	        ]
     ])
	  
          echo "(*******)"
	  echo "Demo1234 ${WebComponent_VersionId}"
	  def newComponentVersionId = "${WebComponent_VersionId}"
	  echo "git commit ${GIT_COMMIT}"

         step($class: 'UploadBuild', 
         tenantId: "5ade13625558f2c6688d15ce", 
         revision: "${GIT_COMMIT}", 
         appName: "JPetStore-Distributed", 
         requestor: "admin", 
         id: "${newComponentVersionId}", 
         versionName: "1.1.${BUILD_NUMBER}"
      )
}
	stage ('Deploy to DEV') {
	step([$class: 'UCDeployPublisher',
		deploy: [ createSnapshot: [deployWithSnapshot: true, 
			 snapshotName: "1.1.${BUILD_NUMBER}"],
			 deployApp: 'JPetStore-Distributed', 
			 deployDesc: 'Requested from Jenkins', 
			 deployEnv: 'DEV', 
			 deployOnlyChanged: false, 
			 deployProc: 'DeployWeb', 
			 deployReqProps: '', 
			 deployVersions: "WebComponent:1.1.${BUILD_NUMBER}"], 
		siteName: 'Launch_Local'])
 }
	
stage ('wait for deploy') {
	sleep 25
	// echo 'Executing HCL One test ...'
	//sh '/var/jenkins_home/onetest/hcl-onetest-command.sh'
 }
stage ('Invoke_Jmeterpipeline') {
            steps {
                build job: 'JmeterDemo'
            }
        }	

}


