---
title: K3d vs k3s vs Kind vs Microk8s vs Minikube
top: false
cover: false
toc: true
mathjax: true
date: 2022-03-21 09:24:10
password:
summary:
tags: [Kubernetes, 翻译, 进行中]
categories:
---
[原文](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/)

在本地运行 Kubernetes 是保证你的应用程序在生产环境中最常用的容器编排平台运行的最好方式。minikube 就是这样的一个本地 Kubernetes 工具。本文提供了一组可选项和一个简单的对比帮助你在使用时做出一个明智的选择。

- [K3S](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#K3S)
- [K3d](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#K3d)
- [Kind](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#Kind)
- [MicroK8S](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#MicroK8S)
- [Minikube](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#Minikube)
- [K3d,K3s,Kind,MicroK8s,and MiniKube: What sets them apart?](https://thechief.io/c/editorial/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube/#K3d,_K3s,_Kind,_MicroK8s,_and_MiniKube:_What_sets_them_apart?)

Kubernetes 是一个由 Google 开发的面对容器应用的自动部署、扩容、管理和编排的开源平台。它提供了跨多个服务器管理容器的简单系统，并具有优异的负载均衡和资源分配，保证每个应用以最佳的方式运行。

尽管 Kubernetes 是为了在云上构建运行的，开发人员出于各种原因需要在本机运行。在本地运行它可以帮助你快速的熟悉这项技术，而不需要纠结它的复杂性或者直接在云上操作带来的成本。在本地运行它是一个玩容器编排的不错的方式。开发者在本地使用它可以缓解开发环境和生产环境带来的差异，并保证应用可以高效的在生产中运行。

然而，本地 Kubernetes 设置要求一个工具帮助你创建。这篇文章概述比较了5种最常使用的工具。

## K3S
![k3s](https://static.thechief.io/prod/images/image_e4sblgn.width-1024.format-webp-lossless.webp)
K3s 是一种轻量级工具，用于在资源匮乏和远端的物联网和边缘设备上运行的生产级别的 Kubernetes 负载。

K3s 帮助你通过虚拟机 VMWare/VirtualBox 在你本机上运行简单、安全、优化后的 Kubernetes 环境。

## K3d
![k3d](https://static.thechief.io/prod/images/image_1_Qcbhj6C.width-1024.format-webp-lossless.webp)
k3d 是一个平台无关的轻量级包装器，在 docker 容器中运行 k3s。它有助于快速运行单个或多个 k3s 集群，无需进一步配置同时保持高可用性模式。

## [Kind]
![kind](https://static.thechief.io/prod/images/image_2_OlK14k7.width-1024.format-webp-lossless.webp)
主要用于测试 Kubernetes, Kind(Kubernetes in Docker) 使用 Docker 容器作为节点，帮助你在本地和 CI 管道中运行 Kubernetes 集群。

它是一个开源的 CNCF 认证的 Kubernetes 安装程序，支持高可用多节点集群并从其源代码构建 Kubernetes 发行版

## [MicroK8S]
![MicroK8S](https://static.thechief.io/prod/images/image_3_AdNKIoS.width-1024.format-webp-lossless.webp)
由 Canonical 创建的 microK8S 是一个 Kubernetes 发行版，旨在运行快速、自我修复和高可用的 Kubernetes 集群。它针对在多个操作系统（包括 macOS、Linux 和 Windows）上快速轻松地安装单节点和多节点集群进行了优化。

它非常适合在云、本地开发环境以及边缘和物联网设备中运行 Kubernetes。它还可以在使用 ARM 或 Intel 的独立系统中高效工作，例如 Raspberry Pi。

## Minikube
![Minikube](https://static.thechief.io/prod/images/image_4_q65N6a7.width-1024.format-webp-lossless.webp?ref=thechiefio)
miniKube 是使用最广泛的本地 Kubernetes 安装程序。它提供了一个易于使用的指南，用于跨多个操作系统安装和运行单个 Kubernetes 环境。它将 Kubernetes 部署为容器、VM 或裸机，并实现了一个 Docker API 端点，帮助它更快地推送容器映像。它具有负载平衡、文件系统挂载和 FeatureGates 等高级功能，使其成为本地运行 Kubernetes 的最爱。

## K3d、K3s、Kind、MicroK8s 和 MiniKube：是什么让它们与众不同？
这些工具中的每一个都为多个平台提供了一个易于使用且轻量级的本地 Kubernetes 环境，但有几件事使它们与众不同。

例如，K3s 提供了一个基于 VM 的 Kubernetes 环境。要设置多个 Kubernetes 服务器，您需要手动配置额外的虚拟机或节点，这可能非常具有挑战性。但是，它是为生产环境而设计的，这使其成为在本地模拟真实生产环境的最佳选择之一。

作为 K3s 的实现，K3d 具有 K3s 的大部分特性和缺点；但是，它不包括多集群创建。 K3s 专门用于在具有 Docker 容器的多个集群中运行 K3s，使其成为 K3s 的可扩展和改进版本。

虽然 minikube 是在本地运行 Kubernetes 的一般不错的选择，但一个主要缺点是它只能在本地 Kubernetes 集群中运行单个节点——这使它离生产多节点 Kubernetes 环境更远一些。

与 miniKube 不同，microK8S 可以在本地 Kubernetes 集群中运行多个节点。

与此列表中的其他工具相比，microK8S 在不支持 snap 包的 Linux 机器上安装具有挑战性。 microK8S 使用 canonical 创建的 snap 包来安装 Linux 机器工具，这使得在不支持它的 Linux 发行版上很难运行。 miniKube 还使用 VM 框架 multipass 安装在多个平台上，为 Kubernetes 集群创建 VM。
