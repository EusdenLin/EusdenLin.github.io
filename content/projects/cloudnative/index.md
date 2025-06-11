---
title: "Lab Requirements Management" 
date: 2024-06-05
tags: ["Cloud Native", "Kubenetes", "Ticket System"]
author: ["Eusden Lin", "Hsuan-yu Peng", "Pei-chun Tsai", "Tony Won", "Trista Lee"]
description: "This is the final project of the Cloud Native in 2024 Spring." 
summary: "Built a ticket-based system (like Jira) for wafer quality management, where users can create requests by choosing labs, setting urgency (e.g., normal, urgent, critical), uploading files, and tracking progress—while lab engineers get prioritized task lists, real-time updates, and tools to approve, complete, or reject tasks with proper permission control." 
cover:
    image: "cover.png"
    alt: "Lab Requirements Management"
    relative: true
editPost:
    URL: "https://github.com/EusdenLin/cn-project/"
    Text: "Github Repository"

---

![System Architecture](./cover.png)

## Introduction

We built a ticket-based system (like Jira) for wafer quality management, where users can create requests by choosing labs, setting urgency (e.g., normal, urgent, critical), uploading files, and tracking progress—while lab engineers get prioritized task lists, real-time updates, and tools to approve, complete, or reject tasks with proper permission control.

### Our Goals:

- Sort/filter requests based on urgency level
- Send real-time email notifications to approvers
- Allow users to complete or reject requests
- Update request status
- Permission control

### Sysem Architecture

* Frontend: Vue.js + nodejs
* Backend: Flask
* Database: mysql
* Mail notification: google smtp
* Monitoring: Grafana


### Monitoring

We leverage Grafana to monitor the k8s clusters:

* Data Requested
* Requested Errors
* Dashboard View Counts
* CPU Usage
* MEM Usage
* Cluster Logs
