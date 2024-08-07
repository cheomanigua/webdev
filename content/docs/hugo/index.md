---
title: "Hugo"
date: 2024-08-07T07:50:53+02:00
draft: false
weight: 100
---

# Installation

## Prerequisites

Although not required in all cases,[Git](https://git-scm.com/), [Go](https://go.dev/), and [Dart Sass](https://gohugo.io/hugo-pipes/transpile-sass-to-css/#dart-sass) are commonly used when working with Hugo.

If you are using Debian or its derivatives, installing **hugo** from the packages repository will install Go and Dart Sass along side hugo:

```
$ sudo apt install hugo
```

## Prebuilt binaries

Visit the [latest release](https://github.com/gohugoio/hugo/releases/latest) and scroll down to the Assets section.
1. Download the archive for the desired edition, operating system, and architecture
2. Extract the archive
3. Move the executable to the desired directory
4. Add this directory to the PATH environment variable
5. Verify that you have execute permission on the file

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

## Themes

In order for a Hugo site to work, there must be at least one theme present in the project. There are two ways to download and add themes for your project:

- Adding a git submodule with the theme, as we did above. [Git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) is used to separate your main project from another project within it, in above case the other project is *theNewDynamic*.
- Cloning the theme directly from *GitHub*:
```
git clone https://github.com/[user]/[hugo-theme].git themes/[hugo-theme]
cp -a themes[hugo-theme]/exampleSite/. .
```
Be sure that your project's `config.toml` file has the theme added:
```
$ echo "theme = 'theme-name'" >> config.toml
```

## Add content
```
$ hugo new content/posts/my-first-post.md
```
Hugo created the file in the `content/posts` directory.

If you open the file with your editor, you'll notice that the file has a [front matter](https://gohugo.io/content-management/front-matter/) already populated. This template is fetched from your project's `archetypes` directory. You can edit the archetype files to customize the front matter.

The archetype files in the root directory will override the archetype files in `themes/[hugo-theme]/

### Front matter
If `draft = true` is present in the front matter for a particular file, Hugo won't publish that file when building the site or running the developer server. You can override that behavior during development by running:
```
$ hugo server -D
```

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
