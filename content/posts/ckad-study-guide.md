---
title: "⛵ My CKAD Study Guide"
date: "2020-07-18"
author: grahamplata
excert: "A guide to help with my preperation for the CKAD exam."
tags: ["k8s", "exam", "ckad"]
---

In the following post I share my thoughts and resources I used in preperation for the CKAD exam.

## Table of Contents

1. [Context](#context)
2. [My Objectives](#my-objectives)
3. [Practice tools and Study Guides](#practice-tools-and-study-guides)
   - [Base Concepts](#base-concepts)
   - [Tools](#tools)
4. [The Exam](#the-exam)
   - [Tips](#tips)
   - [Exam day](#exam-day)

## Context

**_[Certified Kubernetes Application Developer or "CKAD"](https://www.cncf.io/certification/ckad/)_** A certification from the Cloud Native Computing Foundation (CNCF), which validates that users can design, build, configure, and expose cloud native applications for Kubernetes.

**Why did you pursue this certification?**

I pursued this certification because my team at work supports micro service applications via Kubernetes and I felt that this was a logical move. My thought process was that this would be a great way to validate my skill and pass on what I have learned to others.

## My Objectives

- Pass the exam!
- Grasp the concepts in building and maintaining Kubernetes applications.
- Share what I have learned.

## Practice tools and Study Guides

### Base Concepts

#### Certified Kubernetes Application Developer Udemy Course

> I started my studies with this [Udemy course](https://www.udemy.com/course/certified-kubernetes-application-developer/) taught by Mumshad Mannambeth. His course stepped through all concepts that are covered by the current exam. A nice bonus is that there are labs that accompany each lesson and two practice exams.

#### [dgkanatsios' Study Guide](https://github.com/dgkanatsios/CKAD-exercises)

> Dgkanatsios prepared a set of exercises to prepare for Certified Kubernetes Application Developer exam. Available on [github](https://github.com/dgkanatsios/CKAD-exercises)

### Tools

#### [Killer.sh](Killer.sh)

> A test environment built to mimic the real exam. The CKAD Simulator came with 20 scenarios in which you attempt solve in the same 2 hours. When time expires you get access to the solutions and an automated score.

#### [minikube](https://minikube.sigs.k8s.io/docs/start/)

> minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes. This works great with free resources like [dgkanatsios' Study Guide](https://github.com/dgkanatsios/CKAD-exercises) linked above.

#### Vim

> Know enough Vim or nano to get by. My daily driver is Vscode so I had to put in some extra effort and brush up. Knowing to select, delete and copy quickly will help speed up your efficiency.

#### Tmux or Screen

> I didn't feel a need to use any terminal multiplexers.

## The Exam

### Tips

- If you use any extra tools in your day job to make your life easier ([kubens](https://github.com/ahmetb/kubectx)) be mindful that they will not be available on test day.
- Make use of the provided cheatsheet in the [k8s docs](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).
- Proactively make bookmarks when studying to use on exam day. Just make sure to only use one additional tab.
- Use Autocompletion

  - ```shell
    source <(kubectl completion bash)
    echo "source <(kubectl completion bash)" >> ~/.bashrc
    ```

- Alias kubectl to k

  - ```shell
    alias k=kubectl
    complete -F __start_kubectl k
    ```

- The exam happens in the browser and the provided terminal can be laggy and quite clumsy at times.
- `kubectl explain` and `kubectl --help` are the fastest way to get answers if you get stuck.
- If you are having trouble with a question flag it for later.
- For every question you are given a context command. Get in the habbit of running it before each question as you don't want to mix up environments. There are 4.

### Exam Day

Make sure you read the [instructions!](https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad)

The test...

- The CKAD is 20 questions with tasks.
- You have 2 hours to complete the exam.
- Tasks are solved in the command line on Linux.
- The exams are proctored via streaming audio, video, and screen sharing feeds.
- Results will be emailed 36 hours from the time that the exam is completed.

## Closing thoughts

- Practice practice practice. _"Slow is Smooth, Smooth is Fast."_
- My results appeared at the 36 hour mark. Waiting was worse than the preperation.
- Practicing with a clock builds confidence.
- Best of Luck!
