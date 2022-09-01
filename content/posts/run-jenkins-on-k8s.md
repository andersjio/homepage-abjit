---
title: "Run Jenkins on K8s"
date: 2022-09-01T16:12:08+02:00
description: 'Blog post about running Jenkins build nodes on k8s'
draft: false
image: images/cctv.jpeg
---

# Enabling Jenkins to use k8s pods as build nodes
This is the first post about how to configure Jenkins to utilize Kubernetes Pods as build slaves.

The goal of the blog post is to run a simple job, specified with a Jenkinsfile, on a dynamically created pods in Kubernetes.

### prereqs.
* A running Jenkins
* A running Kubernetes cluster