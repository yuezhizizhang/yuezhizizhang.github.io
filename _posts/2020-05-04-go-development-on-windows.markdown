---
layout: post
title:  "Get started with Go Development on Windows"
categories: "golang Windows"
---

### 1. Prepare Windows for Golang and Container development

- [Install Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10){:target="_blank"}
- [Setting Up Docker for Windows and WSL to Work Flawlessly](https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly){:target="_blank"}
- [Sharing SSH keys between Windows and WSL](https://devblogs.microsoft.com/commandline/sharing-ssh-keys-between-windows-and-wsl-2/){:target="_blank"}

### 2. Install and Upgrade Go

- [Install Go on Ubuntu](https://www.linode.com/docs/development/go/install-go-on-ubuntu/){:target="_blank"}
- [How to update the Go version](https://gist.github.com/nikhita/432436d570b89cab172dcf2894465753){:target="_blank"}
- [Go Downloads page](https://golang.org/dl/){:target="_blank"}

### 3. Install GO Tools for VSCode in WSL

- [Open VSCode in WSL](https://code.visualstudio.com/docs/remote/wsl#_open-a-remote-folder-or-workspace){:target="_blank"}
- [Install GO Tools for VSCode in WSL - Troubleshoot](https://github.com/microsoft/vscode-go/issues/2656){:target="_blank"}

```bash
sudo chmod a+w /home/<user>/.cache
```

### 4. Remove Go package installed with "go get"

```bash
sudo rm -rf $GOPATH/pkg/mod/<pkg_name>
```

### 5. Go Module

- [Why is GO111MODULE everywhere, and everything about Go Modules](https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k){:target="_blank"}

To run Go codes outside of $GOPATH/src, use Go Modules:

```bash
go mod init <module_name>
```

### 6. Debug Go in VSCode in WSL

No. You can't debug Go in WSL. It's a known limitation of WSL. It is addressed in WSL2.

- [WSL Known Limitations](https://code.visualstudio.com/docs/remote/wsl#_known-limitations){:target="_blank"}

### 7. Go tutorials

- [Go Introductory Tutorials](https://medium.com/rungo/go-introductory-tutorials-896aeda0fb8a){:target="_blank"}
- [Go by Examples](https://gobyexample.com/){:target="_blank"}

### 8. Go limitations
The limitations I don't like:
 - Go build errors are vague
 - Go packages management is a mess
 - No lambda method
 - No generic collection method, like filter, map, sort
 - No ?: operator
 - No default parameter values for func