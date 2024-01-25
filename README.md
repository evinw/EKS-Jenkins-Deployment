# EKS Jenkins Deployment

## Overview

deploying a web application to AWS EKS using Jenkins CI/CD pipeline.

## Prerequisites

- Jenkins with AWS and Docker plugins installed.
- AWS EKS cluster named 'dev-eks-cluster'.
- Docker image registry and credentials.

## Deployment Steps

1. **Build the Docker Image**: The Jenkins pipeline builds the Docker image and pushes it to the registry.
2. **Deploy to EKS**: Updates the kubeconfig for EKS and applies the Kubernetes manifests.
