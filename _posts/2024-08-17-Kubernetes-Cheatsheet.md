---
layout: post
title: Kubernetes Cheatshet
datetime: 2024-08-17 18:32:00
categories: devops, cheatsheet
---

## Basic commands

This is a list of the basic command operations you are able to do with Kubernetes.

- `kubectl get pods` - List all pods in the current namespace
- `kubectl get pods -n <namespace>` - List all pods in a specific namespace
- `kubectl get pods -o wide` - List all pods in the current namespace with more information
- `kubectl describe pod <pod-name>` - Get detailed information about a pod
- `kubectl run <pod-name> --image=<image-name>` - Create a pod
- `kubectl delete pod <pod-name>` - Delete a pod
- `kubectl edit pod/<pod-name> -o yaml` - Edit a pod in yaml format
