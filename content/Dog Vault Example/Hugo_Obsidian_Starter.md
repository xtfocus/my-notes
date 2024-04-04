---
date: 2023-06-03
authors:
- Tung Nguyen
title: How I created this site
tags: []
categories: []
series: []
---

My OS: Arch Linux

Tools I used:
- Hugo
- Github Pages
- Obsidian



# Step 1: Create a hugo site

## Create the site's directory
```bash
hugo new site best-site-ever
```

This create a new directory named `best-site-ever`.\

```
best-site-ever
├── archetypes
│   └── default.md
├── assets
├── content # your site content sits here
├── data
├── hugo.toml
├── i18n
├── layouts
├── static
└── themes # themes for your site
```

More about this directory structure, see [Hugo's guide](https://gohugo.io/getting-started/directory-structure/#directories) 

## Install a theme

Pick a Hugo theme [here](https://themes.gohugo.io/), install it with `git clone`:

```bash
git clone https://github.com/nanxiaobei/hugo-paper themes/hugo-keepit
```

This will download the repo to themes/hugo-keepit directory (it automatically creates the directory if not exists)

Alternatively, make `best-site-ever` a git repo, then use `git submodule add`

```bash
git init
git submodule add https://github.com/nanxiaobei/hugo-paper themes/hugo-paper
```

## Configure with `hugo.toml`

See a list of configuration options [here](https://gohugo.io/getting-started/configuration/#all-configuration-settings)

Below is a basic `hugo.toml`. 

```
baseURL = 'http://example.org/'
languageCode = 'en-us'
title = 'Best site ever'
theme = "hugo-paper"

[module]
[[module.mounts]] # See https://gohugo.io/hugo-modules/configuration/#module-configuration-mounts
source = 'content'
target = 'content'
excludeFiles = ['templates/**']
```

Your site with `hugo-paper` theme is visible now with 

```bash
hugo serve
```

## Make links work
```bash
mkdir -p layouts/_default/_markup
touch layouts/_default/_markup/render-link.html
```

# Step 2: Make content

Make some posts (Markdown files) under `content/posts/` using Obsidian. 
```
mkdir -p content/posts
```

`beagle.md`
```
title: Beagle
---

# Beagle
A beagle is a [[dog]]
```

`dog.md`
```
---
title: Dogs
---

# Dogs
Dogs are friends.
Some dog breeds include [[beagle|Beagle]], Mastiff
A [beagle](beagle.md) is generally smaller than a Mastiff
```

In two notes above, we use both Wikilinks and Markdown links (about links in Obsidian [here](https://help.obsidian.md/Linking+notes+and+files/Internal+links). When you view the site in the browser, only the Markdown link works.

To convert every wikilink to markdown link, I used `obsidian-export`, which is a Rust util. So, you need Rust toolchain installed in your system, [instruction](https://www.rust-lang.org/tools/install)

```bash
# Install Rust if you haven't already
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Install obsidian-export
cargo install obsidian-export

# Convert wikilinks to markdown in place
obsidian-export content/posts content/posts
```

Your links should be functional now.

# What's next
Your site is able to render text and link properly. But you might want to use a more advanced theme like [hugo-coder](https://github.com/luizdepra/hugo-coder/tree/main). You might also want to custom `layouts` furthermore. Go find out how to do that.

You might also want to showcase your graphview. Check out [Quartz](https://quartz.jzhao.xyz/)

Finally, you may want to publish your site. Hugo has documentation on several hosting manners, Github pages are also [included](https://gohugo.io/hosting-and-deployment/hosting-on-github/)


---
Reference:
- https://quantick.dev/posts/obsidian-hugo/
- https://gohugobrasil.netlify.app/themes/installing-and-using-themes/
- https://gohugo.io/render-hooks/links/
- https://notes.vasuagrawal.com/2022/02/setting-up-a-blog-with-hugo-obsidian-and-cloudflare/
