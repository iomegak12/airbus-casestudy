pipeline {
  agent any
  stages {
    stage('Prepare K8S NS') {
      steps {
        sh '$KUBECTL create namespace $K8S_NS'
      }
    }

    stage('Deployment') {
      parallel {
        stage('Mongo DB') {
          steps {
            dir(path: 'deployment/eks/mongo') {
              sh '$KUBECTL apply -f eks-pv-pvc.yaml -n $K8S_NS'
              sh 'kubectl apply -f eks-mongodb.yaml -n $K8S_NS'
            }

          }
        }

        stage('Rabbit MQ') {
          steps {
            dir(path: 'deployment/eks/rabbitmq') {
              sh '$KUBECTL apply -f rabbitmq.yaml -n $K8S_NS'
            }

          }
        }

      }
    }

    stage('Cleanup') {
      steps {
        input 'Have you Tested the Application? This would initialize clean-up after the confirmation.'
        sh '$KUBECTL delete -f deployment/eks/rabbitmq/rabbitmq.yaml -n $K8S_NS'
        sh '$KUBECTL delete -f deployment/eks/mongo/eks-pv-pvc.yaml -n $K8S_NS'
        sh '$KUBECTL apply -f deployment/eks/mongo/eks-mongodb.yaml -n $K8S_NS'
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
    KUBECTL = '/home/ec2-user/bin/kubectl'
  }
}