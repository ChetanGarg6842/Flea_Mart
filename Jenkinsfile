pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "my-django-app"
        DOCKERFILE_PATH = "./Dockerfile"
        DOCKER_REGISTRY = "docker.io"
        DOCKER_REGISTRY_CREDENTIALS = "docker_cred"
        PROMETHEUS_PORT = 9090
    }

    stages {
        
    //    stage('Checkout') {
    //        steps {
    //            git 'https://github.com/Kartik-Dhoundiyal/Flea_Mart.git'
    //        }
    //    }

        stage('Test') {
            steps {
            // Build the Docker image
                sh 'docker build -t my-django-app-test . -f Dockerfile.test'
        
            // Run the tests inside a Docker container
                //sh 'docker run --rm my-django-app-test'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -f $DOCKERFILE_PATH -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME .'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKER_REGISTRY_CREDENTIALS", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login $DOCKER_REGISTRY -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                    sh "docker push $DOCKER_USERNAME/$DOCKER_IMAGE_NAME"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh "docker run -d --name $DOCKER_IMAGE_NAME -p 8000:8000 $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME"
            }
        }
    }
}
//         stage('Monitor') {
//             steps {
//                 sh 'pip install prometheus-flask-exporter'
//                 sh 'pip install prometheus_client'
//                 sh 'echo "from prometheus_flask_exporter import PrometheusMetrics\nmetrics = PrometheusMetrics(app=None)\nmetrics.start_http_server(5001)" >> app.py'
//                 sh 'docker run -d -p 9090:9090 prom/prometheus'
//                 sh 'docker run -d -p 5001:5001 --name my-django-app $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME python app.py'
                
//             }
//         }

//         stage('Monitor2') {
//             steps {
//                 // Install Prometheus exporters
//                 sh 'pip install prometheus-flask-exporter'
//                 sh 'pip install prometheus_client'

//                 // Start the Prometheus server
//                 sh 'docker run -d --name prometheus -p 9090:9090 prom/prometheus'

//                // Start the Django app with Prometheus monitoring
//                 //sh 'docker run -d --name django-app -p 8000:8000 my-django-app'

//                 // Start the Prometheus exporter for the Django app
//                 sh 'python manage.py prometheus_export'

//                 // Wait for the Django app to start up
//                 sh 'sleep 10'

//                 // Add the Django app to Prometheus configuration
//                 sh 'echo "  - targets: [\'django-app:8000\']" >> /etc/prometheus/prometheus.yml'

//                 // Restart Prometheus to pick up the new configuration
//                 sh 'docker restart prometheus'
//             }
//         }
