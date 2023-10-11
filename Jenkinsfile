pipeline
       {
	   
	   environment{
	    IMAGE_NAME = "alpinehelloworld"
		IMAGE_TAG = "latest"
		STAGING = "cayman-staging"
		PRODUCTION = "cayman-production"	
	   
	   }
	   
	   agent none
	   
	   stages{
	      stage('Build image'){
		  agent any
		  steps{
		    script{
			sh'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
			}
		  }
		  
		  }
		}
	    stage('Run image'){
		  agent any
		  steps{
		    script{
			sh'''docker run -d -p 80:50000 -e PORT=50000  --name ${IMAGE_NAME} ${IMAGE_NAME}:${IMAGE_TAG}  
                 sleep 5
			'''
			}
		  }
		  
		  }
		stage('Test image'){
		  agent any
		  steps{
		    script{
			sh'''curl http://172.17.0.1 | grep -q "Hello world!"
				
			'''
			}
		  }
		  
		  }
		stage('Clean container'){
		  agent any
		  steps{
		    script{
			sh'''
				docker stop ${IMAGE_NAME}
				docker rm ${IMAGE_NAME}
			'''
			}
		  }
		  
		  }
		
		stage('push image in staging and deploy it'){
		  when{
		    expression {GIT_BRANCH == "origin/master"}
		  }
		  agent any
		  
		  environment {
		HEROKU_API_KEY = credentials = ("heroku api key")
		  steps{
		    script{
			sh''' 
			    heroku container:login
				heroku create $STAGING || echo "project already exist"
				heroku container:push -a $STAGING web
				heroku container:release -a $STAGING web
			} 
		  }
		
		}
		}
	   
		stage('push image in production and deploy it'){
		  when{
		    expression {GIT_BRANCH == "origin/master"}
		  }
		  agent any
		  
		  environment {
		HEROKU_API_KEY = credentials = ("heroku api key")
		  steps{
		    script{
			sh''' 
			    heroku container:login
				heroku create $PRODUCTION || echo "project already exist"
				heroku container:push -a $PRODUCTION web
				heroku container:release -a $PRODUCTION web
			} 
		  }
		
		}
		}
		
	   
	   }
	   
