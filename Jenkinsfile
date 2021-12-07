pipeline {
  agent any
  stages {
    stage('Prepare Images') {
      parallel {
        stage('Prepare Calculation Service') {
          steps {
            dir(path: 'source/calculation-offer-service/CalculationServiceAPISolution') {
              sh 'pwd'
              sh 'docker build -t $CALCULATION_SERVICE_IMAGE:latest -t $CALCULATION_SERVICE_IMAGE:$BUILD_NUMBER .'
              sh 'docker tag $CALCULATION_SERVICE_IMAGE:latest $ECR_ID/$CALCULATION_SERVICE_IMAGE:latest'
              sh 'docker tag $CALCULATION_SERVICE_IMAGE:$BUILD_NUMBER $ECR_ID/$CALCULATION_SERVICE_IMAGE:$BUILD_NUMBER'
              sh 'docker login --username $ECR_CREDENTIALS_USR --password $ECR_CREDENTIALS_PSW $ECR_ID'
              sh 'docker image prune -f'
              sh 'docker push $ECR_ID/$CALCULATION_SERVICE_IMAGE:latest'
              sh 'docker logout'
            }

          }
        }

        stage('Prepare Validation Response Daemon') {
          steps {
            dir(path: 'source/creditcard-identity-verification-response-daemon') {
              sh 'docker build -t $VALIDATION_RESPONSE_DAEMON_IMAGE:latest -t $VALIDATION_RESPONSE_DAEMON_IMAGE:$BUILD_NUMBER .'
              sh 'docker tag $VALIDATION_RESPONSE_DAEMON_IMAGE:latest $ECR_ID/$VALIDATION_RESPONSE_DAEMON_IMAGE:latest'
              sh 'docker login --username $ECR_CREDENTIALS_USR --password $ECR_CREDENTIALS_PSW $ECR_ID'
              sh 'docker image prune -f'
              sh 'docker push $ECR_ID/$VALIDATION_RESPONSE_DAEMON_IMAGE:latest'
              sh 'docker logout'
            }

          }
        }

        stage('Prepare Email Service') {
          steps {
            dir(path: 'source/email-service') {
              sh 'pwd'
              sh 'docker build -t $EMAIL_SERVICE_IMAGE:latest -t $EMAIL_SERVICE_IMAGE:$BUILD_NUMBER .'
              sh 'docker tag $EMAIL_SERVICE_IMAGE:latest $ECR_ID/$EMAIL_SERVICE_IMAGE:latest'
              sh 'docker login --username $ECR_CREDENTIALS_USR --password $ECR_CREDENTIALS_PSW $ECR_ID'
              sh 'docker image prune -f'
              sh 'docker push $ECR_ID/$EMAIL_SERVICE_IMAGE:latest'
              sh 'docker logout'
            }

          }
        }

        stage('Prepare Identity Verification Service') {
          steps {
            dir(path: 'source/identity-verification-service') {
              sh 'pwd'
              sh 'docker build -t $IDENTITY_VERIFICATION_SERVICE_IMAGE:latest -t $IDENTITY_VERIFICATION_SERVICE_IMAGE:$BUILD_NUMBER .'
              sh 'docker tag $IDENTITY_VERIFICATION_SERVICE_IMAGE:latest $ECR_ID/$IDENTITY_VERIFICATION_SERVICE_IMAGE:latest'
              sh 'docker login --username $ECR_CREDENTIALS_USR --password $ECR_CREDENTIALS_PSW $ECR_ID'
              sh 'docker image prune -f'
              sh 'docker push $ECR_ID/$IDENTITY_VERIFICATION_SERVICE_IMAGE:latest'
              sh 'docker logout'
            }

          }
        }

        stage('Prepare Credit Card Service') {
          steps {
            dir(path: 'source/creditcard-service') {
              sh 'pwd'
              sh 'docker build -t $CREDITCARD_SERVICE_IMAGE:latest -t $CREDITCARD_SERVICE_IMAGE:$BUILD_NUMBER .'
              sh 'docker tag $CREDITCARD_SERVICE_IMAGE:latest $ECR_ID/$CREDITCARD_SERVICE_IMAGE:latest'
              sh 'docker login --username $ECR_CREDENTIALS_USR --password $ECR_CREDENTIALS_PSW $ECR_ID'
              sh 'docker image prune -f'
              sh 'docker push $ECR_ID/$CREDITCARD_SERVICE_IMAGE:latest'
              sh 'docker logout'
            }

          }
        }

      }
    }

    stage('Prepare K8S NS') {
      steps {
        sh 'kubectl create namespace $K8S_NS'
      }
    }

    stage('Deployment') {
      parallel {
        stage('Mongo DB') {
          steps {
            dir(path: 'deployment/eks/mongo') {
              sh 'kubectl apply -f eks-pv-pvc.yaml -n $K8S_NS'
              sh 'kubectl apply -f eks-mongodb.yaml -n $K8S_NS'
            }

          }
        }

        stage('Rabbit MQ') {
          steps {
            dir(path: 'deployment/eks/rabbitmq') {
              sh 'kubectl apply -f rabbitmq.yaml -n $K8S_NS'
            }

          }
        }

      }
    }

    stage('Cleanup') {
      steps {
        input 'Have you Tested the Application? This would initialize clean-up after the confirmation.'
        sh 'kubectl delete -f deployment/eks/rabbitmq/rabbitmq.yaml -n $K8S_NS'
        sh 'kubectl delete -f deployment/eks/mongo/eks-pv-pvc.yaml -n $K8S_NS'
        sh 'kubectl apply -f deployment/eks/mongo/eks-mongodb.yaml -n $K8S_NS'
      }
    }

  }
  environment {
    ECR_ID = '142198642907.dkr.ecr.ap-south-1.amazonaws.com'
    CALCULATION_SERVICE_IMAGE = 'ramkumarv2-casestudy-calculation-service'
    ECR_CREDENTIALS = credentials('ecr-credentials')
    VALIDATION_RESPONSE_DAEMON_IMAGE = 'ramkumarv2-casestudy-creditcard-identity-verification-response-daemon'
    EMAIL_SERVICE_IMAGE = 'ramkumarv2-casestudy-email-service'
    CREDITCARD_SERVICE_IMAGE = 'ramkumarv2-casestudy-creditcard-service'
    IDENTITY_VERIFICATION_SERVICE_IMAGE = 'ramkumarv2-casestudy-identity-verification-service'
    K8S_NS = 'eks-training'
  }
}