---
title: 'Firebase'
date: 2019-02-11T19:27:37+10:00

---

## Installation

```
$ curl -sL https://firebase.tools | bash
```

## Update
```
$ curl -sL https://firebase.tools | upgrade=true bash
```

## Setup
```
$ firebase login
```

## Commands

- firebase init
- firebase projects:list


## Deploy Hugo site

- Use an existing repository or create a new one
- Within the root directory of the web project, type: `$ firebase init hosting`
- Within the root directory of the web project, type: `$ hugo && firebase deploy`
