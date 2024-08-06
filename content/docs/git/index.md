---
title: 'Git'
date: 2019-02-11T19:27:37+10:00
weight: 1
---

In this tutorial you'll learn how to create and maintain a version control system for your project, as if you just installed the operating system. This tutorial assumes that you already have a project in your local machine.

# Initial Setup

## Overview

1. Create ssh keys in local machine
2. Add public ssh key to GitHub
3. Create GitHub repository
4. Connect local project to GitHub repository
5. Update your repository when code changes 


## 1. Create ssh keys in your local machine
***Skip this step if you already have ssh keys in your local machine***

Generate the ssh keys: `$ ssh-keygen`

By default it will generate the file `~/.ssh/id_rsa`, but you can edit the name. Leave it like that.

You can also choose to generate a passphrase for the private key, which you will need to enter everytime you ssh. Fortunately, you can prevent from entering the passphrase everytime by adding the private key to the ssh-agent. In order to add the private key to the ssh-agent, issue:

`$ ssh-add ~/.ssh/id_rsa`

You will only need to enter the passphrase once when accesing the GitHub repository with **git** during the second `git push` iteration.

The files `id_rsa` and `id_rsa.pub` have been created in the `.ssh` directory. Copy the content of `id_rsa.pub` to the clipboard for the next step.

## 2. Add your public ssh key to GitHub
***Skip this step if you already added your public SSH key to GitHub***

1. Log into GitHub ➡️  Settings ➡️  SSH and GPG keys ➡️  New SSH key
2. Paste the content of your public SSH key into the '**Key**' field. Add a descriptive label for the '**Title**' field.

## 3. Create a GitHub repository to host your project
1. Log into GitHub and click on the '**New**' button 
2. Type in the repository name
3. Add a description
4. No need to add a `README` file or `.gitignore` file. It's better to create in the local machine.
5. Click on '**Create repository**' button
6. Click on the '**Code**' button and choose the 'Local' tab and 'SSH' tab
7. Copy the URL of the repository to the clipboard for the next step by clicking on the 'copy' icon

## 4. Connect your local project to your GitHub repository
1. In your local machine, navigate to your project root directory
2. Create a `.gitignore` file of your liking
3. Then, issue these commands:
```bash
git init
git add .
git commit -m 'first commit'
git branch -M main
git remote add origin [repository URL] 
git push -u origin main
```

## 5. Update your repository when code changes in local project 
When working on your project, you can update the GitHub repository. Issue these commands:

```bash
git add .
git commit -m 'some changes'
git push
```

# Operations

## Git clone

- `git clone`
- Download a whole repository from GitHub
- Used when a new developer is going to work in the project

## Git pull

- `git pull`
- It is the same as `git fetch` + `git merge`
- Updates a local repository to the latest changes from GitHub
- Before pulling, push any local change to GitHub
- It is recommended to pull frequenctly when working within a team
