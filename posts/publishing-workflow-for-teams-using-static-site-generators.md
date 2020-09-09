---
title: A publishing workflow for teams using static site generators
date: '2017-01-02'
tags:
  - cyber
---

If your team is semi-technical, and writes content for a static site generator such Jekyll or Hugo, you may have wondered how to approve new content before deploying it to production.

For solo writers this isn't a problem. You can preview the site locally. But how do you share new content with your team for proof-reading and approval before publishing?

This post shows how you can do that using Hugo, GitHub and Netlify [deploy previews](https://www.netlify.com/blog/2016/07/20/introducing-deploy-previews-in-netlify/).

<!--more-->

A few weeks ago I read a [post on the CircleCI blog](https://circleci.com/blog/how-mapzen-uses-circleci-artifacts-for-static-site-preview-generation/) about a tool called 'precog' that uses the artifacts generated by CircleCI to render a Jekyll site so that changes can be previewed before making a decision to deploy to production.

This is a great idea but has too many moving parts for my liking.

There had to  be a simpler way.

## Enter Hugo, GitHub and Netlify

This blog is hosted by the excellent [Netlify](https://www.netlify.com/) and it turns out they have an option to build previews of sites via [Deploy Contexts](https://www.netlify.com/docs/continuous-deployment/#deploy-contexts). I'll explain how this works below.

[Hugo](https://gohugo.io/) is an excellent choice for static site generation - it's fast, easy to install, and trivial to maintain locally since it's just a Go binary. (I've been burned before trying to keep my Node / Python / Ruby static site generators up-to-date. Publishing using a static site generator needs to be low friction.) This makes Hugo a great choice for teams since it's easy to get everyone set up with the same environment.

To get your site published by Netlify you'll need to push your static site to GitHub and connect it to your Netlify account. Here's the only configuration I made within Netlify for this site:

<img title="Netilfy config" src="/img/2017/netlify-config.png">

This tells Nelify to build a new version of the site by running the `hugo` command when a commit is made to the master branch. You can view the [source of my blog on GitHub](https://github.com/keybits/keybits.net). Note that there is no generated site in a `public` folder since this is ignored via `.gitignore`. The generated site only occurs on the local development machine and on Netlify.

## Team publishing workflow to allow for review and approval of new content

Here's how the team publishing flow would work for a site using Hugo:

1. Create a new branch, e.g: `git branch -b product-launch-posts`

2. Create some new content locally and commit the changes

3. Push the new branch to GitHub and create a pull request

4. As you can see in the screenshot below, Netlify generates a site preview so anyone reviewing the PR can see how the new content will look:

<img title="Netlify preview" src="/img/2017/netlify-preview.png"></a>

5. After review, merge the PR into `master` and the site will be live.

If required by your team, you can [protect the master branch](https://help.github.com/articles/about-protected-branches/) on GitHub and require review approval before the PR can be merged.

For all this to happen, there's just one bit of configuration needed. Create a `netlify.toml` file at the root of your Hugo site with the following:

```bash
[context.deploy-preview]
  command = "hugo --buildDrafts --buildFuture --baseURL /"
```

This tells Netlify to build your Hugo site with drafts and future posts when generating a preview. The `baseURL` is specified with a `/` to override the production site baseURL you'll have in Hugo's `config.toml` This will make the links on the preview site work correctly.

## Elegant and simple

I really like the workflow above. Once set up your team can use familiar tools (GitHub pull requests) to get a link to a full preview of what the site will look like before deploying to production. It's easy to comment, review and amend the PR and you don't have to worry about managing any servers or hosting.

Netlify has a lot of great features - I recommend checking out [their documentation](https://www.netlify.com/docs/).