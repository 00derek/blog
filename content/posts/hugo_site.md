---
title: "Create blog with GH pages/Hugo"
date: 2020-11-11T20:35:52-08:00
draft: false
toc: true
images:
tags:
  - notes
  - Hugo
  - Github
---

## Github setup
I have a corporate account and wanted to use my personal account for hosting my blog.  So essentially it could be using 2 different SSH key and auth with separate keys -- since I have my company auth with OSXKeychain, I just did it for my personal one, and it will be the same for another account if I need it
- Create SSH keys
  ```
  $ cd ~/.ssh
  $ ssh-keygen -t rsa -b 4096 -C "your_personal_email@domain.com"
  # getting 2 files
  # id_rsa_personal
  # id_rsa_personal.pub
  ```
- Add SSH key to Github account
  ```
  $ pbcopy < ~/.ssh/id_rsa_personal.pub
  # On github
  > Settings -> SSH and GPG keys -> New SSH key -> then paste the personal key
  ```
- edit `.ssh/config`
  ```
  # Personal account - default config
  Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal
  ```
- add SSH keys
  ```
  # ssh-add -- adds private key identities to the authentication agent

  # remove prev. saved identities if needed
  $ cd ~
  $ ssh-add -D

  # add
  $ ssh-add id_rsa_personal

  # list
  $ ssh-add -l
  ```

- talk to Github with ssh
  ```
  git clone git@github.com:00derek/blog.git
  ```

- config git config for my personal repo (under the directory)
  I kept the global settings for the work, and overwrite local config with my personal ones
  ```
  git config user.name "Derek Chang"
  git config user.email "derekchang@live.com"
  ```

## Hugo and themes
Basically follow the [Hugo quickstart](https://gohugo.io/getting-started/quick-start/)

- Install Hugo \
`brew install hugo`
- Create a New Site \
`hugo new site SITE_NAME`
- Add a Theme -- there are plenty of [themes](https://themes.gohugo.io) to choose from
  ```
  cd SITE_NAME
  git init
  git submodule add https://github.com/rhazdon/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
  ```

- add posts
  ```
  hugo new posts/SOME_POST.md
  hugo new about.md
  ```
- run locally with drafts enabled
  `hugo server -D`, now the site is available at [localhost:1313](localhost:1313)
- customize the theme \
  change `config.toml` -- full details of [my config](https://github.com/00derek/blog/blob/master/config.toml)

  ```
  baseurl      = "https://00derek.github.io"
  title        = "My Blog"
  languageCode = "en-us"
  theme        = "hello-friend-ng"
  paginate     = 10
  ```
  [Detailed How-to](https://gohugo.io/hugo-modules/theme-components/)
- Build
  `hugo -D` or `hugo -t hello-friend-ng`

- Adding utterance for commenting
  It's a lightweight comments widget built on GitHub issues. Use GitHub issues for blog comments.
  https://utteranc.es

  - Just need to install at https://github.com/apps/utterances
  - Select the `USERNAME` repo for access
  - Copy the script (based on the configuration) to the template of the theme, e.g.
  `layouts/partials/footer.html` or `layouts/posts/single.html`
  There is probably not a right/better way to do it -- https://gohugobrasil.netlify.app/themes/customizing/
    ```
    <script src="https://utteranc.es/client.js"
            repo="[ENTER REPO HERE]"
            issue-term="pathname"
            theme="github-dark"
            crossorigin="anonymous"
            async>
    </script>
    ```

## Deploy Hugo as a GitHub Pages project

https://gohugo.io/hosting-and-deployment/hosting-on-github/

### Step-by-step
 [Hugo host on Github Step-by-Step](https://gohugo.io/hosting-and-deployment/hosting-on-github/#step-by-step-instructions) is pretty awesome.  Shamelessly copy/paste here.  Just note that `YOUR-PROJECT` repo is different from `USERNAME` repo that going to host github pages are different repos.  I misread that and was wondering why the instructions not working.

- Create a `<YOUR-PROJECT>` (e.g. blog) repository on GitHub. This repository will contain Hugo’s content and other source files.
- Create a `<USERNAME>.github.io` GitHub repository. This is the repository that will contain the fully rendered version of your Hugo website.
- `git clone <YOUR-PROJECT-URL> && cd <YOUR-PROJECT>`
- Paste your existing Hugo project into the new local <YOUR-PROJECT> repository. Make sure your website works locally (hugo server or hugo server -t <YOURTHEME>) and open your browser to [http://localhost:1313](http://localhost:1313).
  - Once you are happy with the results:
  - Press Ctrl+C to kill the server
- Before proceeding run rm -rf public to completely remove the `public` directory
- `git submodule add -b master https://github.com/<USERNAME>/<USERNAME>.github.io.git public`. This creates a git submodule. Now when you run the hugo command to build your site to public, the created `public` directory will have a different remote origin (i.e. hosted GitHub repository).
- Make sure the baseURL in your config file is updated with: <USERNAME>.github.io

### create a deployment script
Same, copy the instructions from [Hugo documentation](https://gohugo.io/hosting-and-deployment/hosting-on-github/#put-it-into-a-script)
- adding a deploy.sh
  ```
  #!/bin/sh

  # If a command fails then the deploy stops
  set -e

  printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

  # Build the project.
  hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

  # Go To Public folder
  cd public

  # Add changes to git.
  git add .

  # Commit changes.
  msg="rebuilding site $(date)"
  if [ -n "$*" ]; then
    msg="$*"
  fi
  git commit -m "$msg"

  # Push source and build repos.
  git push origin master
  ```