---
title: "Hugo"
date: 2024-08-07T07:50:53+02:00
draft: false
weight: 100
---

# Installation

## Prerequisites

Although not required in all cases, [Git](https://git-scm.com/), [Go](https://go.dev/), and [Dart Sass](https://gohugo.io/hugo-pipes/transpile-sass-to-css/#dart-sass) are commonly used when working with Hugo. The following two methods to install '**Hugo**' also take care of the necessary dependecies.

## Methods

### 1. Prebuilt binaries (latest)

Visit the [latest release](https://github.com/gohugoio/hugo/releases/latest) and scroll down to the Assets section. If you are using Debian or its derivative distros, follow these steps:

1. Download the last extended .deb package: `hugo_extended_0.131.0_linux-amd64.deb`
2. Run:
```
$ sudo dpkg -i hugo_extended_0.131.0_linux-amd64.deb
```

### 2. Repository packages (outdated)

If you are using Debian or its derivative distros, run:

```
$ sudo apt install hugo
```

# Create a site

```
hugo new site my-site
cd my-site
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> config.toml
hugo server
```

`hugo server` runs the site in developer mode.

Visit your Hugo site at [http://localhost:1313](http://localhost:1313)

# Themes

In order for a Hugo site to work, there must be at least one theme present in the project.

### Add a theme

There are four different methods to download and add themes to your project: Git clone, Git submodule, Zip file and Hugo module. 

##### 1. Git Clone
```
$ git clone https://github.com/[user-account]/[hugo-theme].git themes/[hugo-theme]
$ cp -a themes[hugo-theme]/exampleSite/. .
```
If you want to update the theme:

```
$ cd themes/[hugo-theme]
$ git pull
```

##### 2. Git Submodule

```
$ git init
$ git submodule add https://github.com/[user-account]/[hugo-theme].git themes/[hugo-theme]
```

When using CI/CD for Hugo website deployment, it’s essential to ensure that the following command is executed before running the `hugo` command.

```
$ git submodule update --init --recursive
```

If you want to update the theme:

```
$ git submodule update --remote --merge themes/[hugo-theme]
```

[Git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) is meant to separate your main project from another project within it. In above case the other project is the theme repository.

##### 3. Download an unzip

Download the theme as a **zip** file. Extract in the directory `themes/[hugo-theme]`.

##### 4. Hugo module (preferred)

- Initialize your own hugo module system:
```
$ hugo mod init github.com/[your_user]/[your_project] 
```

- Add the Theme Module
```
$ hugo mod get github.com/[user-account]/[hugo-theme] 
```

- Configure the theme by adding it in your `hugo.toml` file:
```
theme = "github.com/[user-account]/[hugo-theme]"
```
If you want to update the theme:
```
$ hugo mode get -u github.com/[user-account]/[hugo-theme]
```

More information about Hugo Modules: [https://gohugo.io/hugo-modules/use-modules/](https://gohugo.io/hugo-modules/use-modules/)


### Set the theme in your config file

In the first three methods, be sure that your project's `config.toml` file has the theme added:
```
$ echo "theme = 'theme-name'" >> config.toml
```
or
```
$ echo "theme = 'theme-name'" >> hugo.toml
```


### Overriding a theme

In Hugo, layouts can live in either the project’s (root) or the themes’ layout folders. Any template inside the root layout folder will override the theme’s layout that is relative to it.

For example: `layouts/_default/baseof.html` will override `themes/[hugo-theme]/layouts/_default/baseof.html`. So, you can easily customize the theme without editing it directly, which makes updating the theme easier.

## Add content

```
$ hugo new about.md
```
Hugo created the file in the `content/` directory.

```
$ hugo new posts/my-first-post.md
```
Hugo created the file in the `content/posts/` directory.

If you open the file with your editor, you'll notice that the file has a [front matter](https://gohugo.io/content-management/front-matter/) already populated. This template is fetched from your project's `archetypes` directory. You can edit the archetype files to customize the front matter.

The archetype files in the root directory will override the archetype files in `themes/[hugo-theme]/

If you run `hugo server`, your new post does not appear. Why? See the next section to find out.

### Front matter
If `draft: true` is present in the front matter for a particular file, Hugo won't publish that file when building the site or running the developer server. You can override that behavior during development by running:
```
$ hugo server -D
```

Or you just can delete `draft: true` altogether or set it to `false` and run `hugo server` by itself.


## Running the site in developer mode

During development, you can visualize instantly the changes you make while editing your files every time you save your files. For that you have to run your site in developer mode:

```
$ hugo server
```

## Publish the site

Publishing a site does not deploy it. Hugo creates the entire static site in the `public` directory in the root of your project. This includes the HTML files, and assets such as images, CSS files, and JavaScript files.

```
$ hugo
```

## Deploying the site

You can upload your site directly from your local machine to a cloud provider by running:

```
$ hugo deploy
```
