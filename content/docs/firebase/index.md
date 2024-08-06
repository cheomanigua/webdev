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

## Authentication
```
$ firebase login                            // first time login after installation
$ firebase login:add another@email.com      // add another account
$ firebase login:use this@email.com         // switch between accounts
```

## Commands
```
firebase init hosting
firebase init hosting:github  // set up github connection if it was not set during 'firebase init hosting'
firebase projects:list
firebase hosting:channel:list
firebase hosting:channel:deploy foo --expires 2d
firebase hosting:channel:delete foo 
```

## Deploy Hugo site

- Use an existing repository or create a new one
- Within the root directory of the web project, type: `$ firebase init hosting`
- Within the root directory of the web project, type: `$ hugo && firebase deploy`
