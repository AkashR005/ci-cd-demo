name: CI/CD Pipeline

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1

    - name: Build and push Docker image
      run: |
        docker build -t ci-cd-demo:latest .
        docker save ci-cd-demo:latest -o ci-cd-demo.tar

    - name: Set up Minikube
      uses: medyagh/setup-minikube@latest

    - name: Load Docker image into Minikube
      run: |
        minikube image load ci-cd-demo:latest

    - name: Deploy to Kubernetes
      run: |
        kubectl apply -f k8s/deployment.yaml
        kubectl apply -f k8s/service.yaml
