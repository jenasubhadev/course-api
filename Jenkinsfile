@Library('dxp-pipeline-library')_
node {
	def mvnHome	
	def methods = new libraryFunctions() 

	stage ('clean') {
		
		sh 'bash stopContainer.sh' 		
		sh 'docker system prune -a --volumes -f'
		sh 'docker container prune -f'
		sh 'docker image prune -a -f'
		echo "~~~docker cleaning done ~~~~~"
	}
	stage('Preparation') { // for display purposes
		// Get some code from a GitHub repository
		def repo = "https://github.com/suswan-mondal/course-api.git"		
		checkoutFromRepo(repo)

	}
	stage('Build') {

		// Get the Maven tool.
		mvnHome = tool 'mvn3.6'

		// Run the maven builds		
		mavenBuild(mvnHome)
		sh 'cp /var/lib/jenkins/workspace/dxpcommerce/target/course-api-0.0.1-SNAPSHOT.jar /var/lib/jenkins/workspace/dxpcommerce' 
		
	}
	stage ('DockerBuild Image'){
		echo "~~~~~ DockerBuild Images~~~~"
		//sh 'cd groovy/'
		sh 'pwd'			
		
		app = docker.build("suswan/course")		 
		docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
        //app.push("${env.BUILD_NUMBER}")
		app.push("1.0")
        // app.push("latest")
        } 
		
		echo "~~~~~ push to docker hub done~~~~"
	}
	stage('DockerBuild run'){
		echo "~~~~~ DockerBuild deploy~~~~"
		sh 'nohup ./runContainer.sh > /dev/null 2>&1 &'	
	}
	stage('Deploy'){
		echo "~~~~~ deploy~~~~"
		//bat "set"		
		def jarFileName='course-api-0.0.1-SNAPSHOT.jar'
		//deploy(jarFileName)
				
	}
	stage('Results') {
		// junit '**/target/surefire-reports/TEST-*.xml'
		archive 'target/*.jar'
	}
}