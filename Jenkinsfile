---
pipeline {
    agent any

     environment {
        AWS_ACCESS_KEY_ID     = AKIA2VMZNIE7YT4O4GIK
        AWS_SECRET_ACCESS_KEY = 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
        nameSpace="prod"
        eks_cluster="eks"
        aws_region="ap-south-1"
        lb_link="http://aa590a93012e74343a1584c8df6b0e6b-1905168737.ap-south-1.elb.amazonaws.com/files"
        efs_vol="efs/files/"
    }
    
    stages {
        stage('Build') {
            steps {
                sh '/usr/local/src/apache-maven/bin/mvn clean install'
            }
        }
        stage('Preparing volume for Containers') {
            steps {
                sh 'sudo cp -rf ${WORKSPACE}/webapp/target/webapp ${efs_vol}'
            }
        }
        
        stage('Deployment') {
            steps {
                script {
                    dir('kubernetes') {
                        sh "pwd"
                        sh 'ls -la'
                        sh 'aws configure set region ${aws_region}'
                        sh 'aws eks update-kubeconfig --region ${aws_region} --name ${eks_cluster}'
                        sh 'curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl'
                        sh 'curl -o kubectl.sha256 https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl.sha256'
                                               

                        def mycode = sh(returnStatus: true, script: "openssl sha1 -sha256 kubectl;chmod +x ./kubectl;mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH;echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile;kubectl get deployment -n ${nameSpace}| grep frontend")
                        
                        //println(mycode.getClass())
                        def key1 = mycode.toString()

                        if (key1 == "0") {
                        
                            echo "Deployment already exist"
                            echo "Reconfiguring it as per new changes."
                            
                            sh "chmod +x ./kubectl;mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH;echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile;kubectl version --short --client;kubectl apply -f deployment.yaml --record;kubectl get deployment -n ${nameSpace}"
                            
                            }
                            else {

                                sh "chmod +x ./kubectl;mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH;echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile;kubectl version --short --client;kubectl create -f deployment.yaml --record;kubectl get deployment -n ${nameSpace}"
                            }

                            def mycode2 = sh(returnStatus: true, script: "openssl sha1 -sha256 kubectl;chmod +x ./kubectl;mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH;echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile;kubectl get svc -n ${nameSpace} | grep myfrontend-service")

                            //println(mycode2.getClass())
                            def key2 = mycode2.toString()

                            if (key2 == "0") {
                            
                            echo "LoadBalancer Service already Created"
                            //sleep(3)
                            echo "Mapped it to your current deployment"
            
                            }
                            else {
                                echo "Launching the LoadBalancer Now"
                                sh "openssl sha1 -sha256 kubectl;chmod +x ./kubectl;mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH;echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile;kubectl version --short --client;kubectl create -f LoadBalancer-service.yml -n ${nameSpace}; kubectl get svc -n ${nameSpace}"
                            }
                            sh 'pwd'
                            sleep(5)
                            echo "Please browse below URL for the PROD APP Service"
                            sh "curl -kv ${lb_link}"
                        
                    }
                }
            }
        }
    }


}