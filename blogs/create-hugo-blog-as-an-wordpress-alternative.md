---
title: "Create an Overpowered Hugo Blog (as an WordPress Alternative)"
description: Discover the power of blogging with Hugo! Explore a WordPress alternative
  offering ease and efficiency. Learn to set up Hugo, leverage GitHub Actions
  for automation, and utilize Forestry as a CMS. Elevate your blog with Hugo’s
  simplicity and GitHub’s power.
timestamps:
  publishedOn: 2021-02-21T00:00:00+00:00
coverImage:
  url: https://ik.imagekit.io/jarmos/create-overpowered-hugo-blog.png?updatedAt=1702974989343
  alt: Create an Overpowered Hugo blog (as an WordPress Alternative)
---

A developer without a blog is like a fisherman without their fishing rod. While
it is possible to fish with just a thread & a hook, the rod makes life much
easier for the fishermen. In that context, Dan Bader summed it up well in his
article — [_3 Reasons Why You Need a Programming Blog_](https://dbader.org/blog/3-reasons-why-you-need-a-programming-blog).

But, if you’ve read some of my previous articles, you would know choosing the
right tools to blog is no easy task. I wrote about such a dilemma I was in
earlier. Here’s the
article — [_Medium vs Static Site Generators — A Computer Vision Engineer’s Dilemma_](https://jarmos.netlify.app/posts/medium-vs-static-site-generators).
Since publishing that article I had time to experiment with existing blogging
tools. And I reviewed each of them. You can read the review
at — [_A Review of Some of the Most Popular Static Site Generators_](https://jarmos.netlify.app/posts/reviewing-popular-static-site-generators).

Suffice to say, Static Sites Generators did rescue me out of my dilemma.

Static sites also come with the added benefit of never having to worry about
server maintenance & security updates. They’re a much better alternative to
Wordpress. Besides, they cost almost nothing to produce. You could spend some
cash maintaining it, but the financial costs will never be above a few bucks.

So, in this article, I’ll show how you can set up a blog with Hugo. The blog
will have a CMS, will update itself & some more OP super powers to add along. So
then, without further adieu, let’s dive into setting it up as a Wordpress
alternative.

But do note, having some prior programming knowledge can be a bonus point but is
not a prerequisite. You can follow along with the instructions suggested in this
article & you’ll be fine.

### Prerequisite Tools

The star of our article is [Hugo](https://gohugo.io/). This piece of software is
without a doubt one of the
[_easiest to use_](https://jarmos.netlify.app/posts/reviewing-popular-static-site-generators/#hugo-the-fastest-static-site-generator-ever-created)
Static Site Generator available.

Besides Hugo, we’ll also need a GitHub account to host the articles & other
tools as well. One of those tools include
[GitHub Actions](https://docs.github.com/en/actions). Of the many
community-maintained GitHub Actions, we’ll be using a couple of them.

But no blog can be a Wordpress alternative without a Content Management System
(CMS). So, to deliver onto our CMS needs, we’ll use the services from
[Forestry](https://forestry.io/).

And at last, we’ll use [Netlify](https://www.netlify.com/) services to deliver
the generated static content. They use a global Content Delivery Network (CDN)
to deliver the content to our audiences.

Optionally, we could use
[Google Analytics (GA)](https://analytics.google.com/analytics/web/) for
tracking needs. But if you’re a privacy concerned citizen, use
[Cloudflare Analytics](https://www.cloudflare.com/analytics/) instead.

So, to sum up, here’s the list of all the necessary tools & services we’ll be
using:

- A Github repository to host the articles, themes & other files required by the
  tools we’ll be using.
- A couple of GitHub Actions.
- An account on [Forestry](https://forestry.io/) for the CMS.
- A Netlify account to deliver the static content to our audiences.
- And the optional Google/Cloudflare Analytics for tracking needs.

So, that said, ensure you’re set up with all the tools mentioned above & then we
can get started.

### Using Hugo (With All Its Glory)

As mentioned earlier, Hugo is without a doubt one of the easiest Static Site
Generators available to use. The fact that Hugo has
[50K+ stargazers](https://github.com/gohugoio/hugo/stargazers) shows how useful
it is!

So, without wasting any more time, let’s dive into seeing how to use Hugo.

### Installing Hugo

First things first, is to install Hugo on your local machine. Unlike other
Static Site Generators Hugo has no dependency! But generators like
[Pelican](https://getpelican.com/), [Gatsby](https://www.gatsbyjs.com/) and/or
[Jekyll](https://jekyllrb.com/) needs a local Python/Ruby runtime or a
JavaScript environment. With Hugo, all you need is the
[Hugo binary](https://github.com/gohugoio/hugo/releases) & you’re good to go.

The “ _installation_” procedure is also pretty simple. You need to add the
binary to system `PATH` & invoke the `hugo` command from anywhere in the
Terminal.

Do note, editing each Operating System’s PATH is quite different. Hence, do take
a thorough look at the official
[installation docs](https://gohugo.io/getting-started/installing/).

Now ensure you installed Hugo by running the `hugo version` command on your CLI.
If everything works fine, you should see a similar output.

Output of “hugo version” Command

### Creating Your Site

I must say, installing Hugo while easy, it isn’t the most fun part of the whole
process. So, let’s get started with the fun parts.

You can generate a skeleton site (_which we’ll build upon soon_) with the
`hugo new site .` command. Notice the `.` ( _dot_) at the end of the command? It
tells Hugo to generate the skeleton site in the current directory.

The command generates a simple set of files & folders. Each of them serves a
specific purpose for Hugo. Here’s what the directory structure looks like after
generating them.

Hugo Directory Structure

There’s more to what Hugo generates as a skeleton site than what I can discuss
in one blog. For more info, do take a brief look at the documentation on Hugo’s
[project directory structure](https://gohugo.io/getting-started/directory-structure/).

Without delving into the details of the directory structure, let’s keep things
minimal. That way, there’ll be room for further personalization. And you can
start delivering content at the earliest as well.

### Creating Content (The Actual Fun Stuff!)

As mentioned earlier using Hugo is drop-dead easy. Yet, any given Hugo project
can be as complicated as it needs to be. But regardless, you can start writing
content right away, all you now need is;

- Download a theme under the `themes` directory.
- Configure Hugo to generate your site with some site metadata for SEO needs in
  the `config.yml` file.
- Write content in Markdown under the `content` directory.

It’s that simple!

Hugo boasts of some 300+ beautiful
[community maintained themes](https://themes.gohugo.io/). You can download a
theme to your site’s `theme` directory & Hugo will use it to generate the
contents of your site. And the easiest way to install a theme is to use " _Git
Submodules_ ". That way you can ensure the themes are updated as & when the
theme authors pushes a commit to its repository.

Installing themes is also very easy. Running the
`git submodule add <DOWNLOAD-LINK> themes/<THEME-NAME> --depth=1` command is
enough.

For SEO needs, you might also need site metadata. And Hugo takes care of it
out-of-the-box. You’ve to configure Hugo with a `config.yml` file. And in that
file you include the necessary details for the metadata. Hugo uses those values
to populate the metadata of your site.

And to get a general feel of what a config.yml file looks like, here’s an
example;

Example Hugo Config File

Do note, configuring the `config.yml` isn't limited to what I mentioned here.
Based on the theme you're using, you might/mightn't have to extend on it.

And at last, the content directory where you store the Markdown files for your
articles. You can organize your Markdown content into subdirectories for easier
maintenance & organization. For example, here’s how mine looks.

Example Hugo Contents Directory Structure

The [Content Organization](https://gohugo.io/content-management/organization/)
section of the docs has more details on the topic. Do check it out for more
information.

And with that information, you can start creating content right away.

Be sure to store all the articles in Markdown format under the content
directory. And if you’re not familiar with Markdown, check out
[Markdown Guide](http://markdownguide.org/). Else read ahead to figure out how
to use the Forestry CMS.

Now that you’ve started creating content, it’s time for your audience to read
them! And the next section is about delivering the content to our audiences..

### Deploying the Blog

Unlike Wordpress sites, static sites don’t need a hosting provider (_or a
server_). This alleviates the surface area of potential security & maintenance
mishaps. But without a server to host the content how do we deliver them to our
audience?

This is where Netlify’s services come handy. They’ll deliver our static content
over their global CDN. And their free tier services also come with some nifty
goodies. Some of those extra goodies include a domain, a build platform & many
more. But for reasons mentioned later, we won’t use all their services.

So, ensure you’ve created a [new repo](https://repo.new) to host your site
contents & a [Netlify account](https://www.netlify.com/) to start with. You’ll
need to perform a one-time operation on the Netlify dashboard to create a new
site. Refer to the Netlify docs to know the
[exact process](https://docs.netlify.com/site-deploys/create-deploys/#deploy-with-git)
to do so.

Then push your fresh new site to the GitHub repo, you created. And that should
trigger a
[Netlify build operation](https://docs.netlify.com/configure-builds/get-started/).
You can now view your website at an URL provided by Netlify ( _you can change it
though_ 😉).

Now each time you write a new article or push some aesthetic changes to your
site, Netlify will trigger a build. While it works & is fine for most users,
there are certain downsides. One of them is the slow & unoptimized build timings
( _often taking over 2mins to complete the build_). And considering Netlify
provides only 300 minutes of build per month, it won’t be long before you run
out of it.

Besides, that way you’re not utilizing Hugo’s full potential. Which is why we’ll
use some GitHub Actions instead. With it, we’ll deploy our site & automate some
monotonous tasks as well!

The next section delves deeper into why & how we can achieve such a feat.

### Setting Up Automation & a CMS Backend

Creating & deploying the site was easy & fun. But it doesn’t last long (_saying
from personal experience_). More so, when you add the theme as a submodule. You
can expect frequent updates for it.

But thanks to [GitHub’s Dependabot](https://github.com/dependabot), we can keep
our theme(s) updated at all times.

There’s also the need for a Content Management System (CMS). What good is a
Wordpress Alternative, without a CMS, right? And we should also delegate the
build process to GitHub Actions instead of Netlify. This is a more efficient way
to deploy & maintain our site.

So that said, let’s list out the remaining features we need to work on & start
working on them one-by-one.

- Setup & configure Dependabot to keep all dependencies for our site updated.
- Setup & use Forestry as the CMS backend.
- Configure & delegate the build process to GitHub instead of Netlify.

GitHub’s Dependabot integration came as a godsend. Because of it, keeping
project dependencies up-to-date has never been easier. And guess what?
Dependabot can take care of Git Submodules as well! Which means your themes will
stay updated as soon as its author pushes some changes to it.

And to configure Dependabot, you need a specific config file for it. This
specific file named `dependabot.yml` stays under the `.github` directory (
_create it if it doesn't exist yet_).

And this is how it should look like:

Dependabot Configurations for GitHub Actions & Submodules

With Dependabot configured, it’ll now look for updates every day at 0600 Hours
UTC. And if there’s any, it’ll then open a PR with respective labels for GitHub
Actions & Git Submodules each.

But that’s pretty much the tip of the iceberg for Dependabot configuration. You
can find detailed
[_Dependabot Configuration Options_](https://docs.github.com/en/github/administering-a-repository/configuration-options-for-dependency-updates)
in the official docs.

With Dependabot set up, you no longer have to worry about manual update &
maintenance tasks.

### Setting Up Forestry as a CMS Backend

No static blog can become a true _Wordpress alternative_ without a CMS. And
that’s what makes Wordpress attractive to most users without any technical
prowess. And as such, some users might not be comfortable writing their content
in Markdown. For them a web-based
[Rich Text Format](https://en.wikipedia.org/wiki/Rich_Text_Format) environment
is what works best.

Hence, we’ll be using Forestry’s backend CMS services. There are alternatives
like [Contentful](https://www.contentful.com/) (_one of the most popular one_) &
[Netlify CMS](https://www.netlifycms.org/). But, from personal experience I
found Forestry to be the most easy to setup & configure.

The things you can do with Hugo & Forestry is beyond the scope of this article.
I could write another full-blown article on this topic alone. So, keeping this
article rather short & act as a guide for interested readers, here’s a gist of
how to set up Forestry.

1.  Ensure you’ve Forestry account & your site is “_added_” to their services.
2.  Configure the settings of the CMS from the Admin panel.
3.  Login to your site’s Admin panel by navigating to `<YOUR-SITE-URL/admin>` &
    start writing your content in Rich Text (_you can write in Markdown as well
    if you prefer_).

Forestry has a [guide](https://forestry.io/docs/guides/developing-with-hugo/) to
set up their CMS with Hugo. Do check it out as well!

### Automation & Delegating Build Task to GitHub

While there’s nothing wrong in using Netlify to build the site, it has some
limitations. A major one being, it’s slow & unoptimized build times (_it took
over 2mins for my site to build_). And with Netlify’s limited 300 build mins per
month, it won’t take long before the free quota is used up.

So, to make the most out of Hugo’s potential, you should use GitHub Actions.
With it, you can build your site in <1 mins ( _usually takes 20–40 secs on
average for mine_).

Besides, compared to Netlify, GitHub provides 2000 build mins per month! So,
coupled with less build times & a huge build mins quota, you won’t have to worry
about builds not triggering 😊.

Also, having all tools under one roof makes maintenance much easier to handle.
So, let’s peruse through what we need to do automate some of the build tasks;

1.  First thing first is to ensure you’ve a `netlify.toml` config file under the
    root directory. The Netlify GitHub Action will use it to deploy the website
    to Netlify's CDN. The file usually looks something like the following;

Example Netlify Configurations

There are more details about the file in the
[Netlify Config file](https://docs.netlify.com/configure-builds/file-based-configuration)
documentations.

2\. To automate some more monotonous tasks, add a `build.yml` workflow file.
Place it under the `.github` directory. And add the following content to the
`build.yml` file.

Automated Netlify Build & Deployment with GitHub Actions

This file instructs GitHub Actions to use two workflows for deployment tasks.
It’ll set up Hugo, generate the static content & deploy it to Netlify. Further,
it’s triggered on every push & PR event.

4\. And finally, the best piece of automation fun. Automating PR merges opened
by Dependabot.

Remember the `automerge` labels in the Dependabot config file? This is where
they come handy. We'll use
`[pascalgn/automerge-action](https://github.com/pascalgn/automerge-action)` to
merge any Pull Requests with the label `automerge`. No need for manual
intervention.

And guess what? Dependabot is also configured to update every PR it opens with
that label! 😆

And thanks to all the automation features, you no longer have to even open the
repository ever again! Automation FTW indeed! 🤣

### Final Words

Phew, that was quite a long read for a tutorial! And if you read it till the
end, then thanks for being thorough with it, much appreciated.

You might’ve also noticed, the article isn’t as comprehensive. Yet it touches
upon all the optimal techniques & tools used to maintain a programming blog. So,
I hope the information I shared through this article was enough to get you
started with your blog as well! If you do so, reach out to me & say “ _Hi!
Here’s my blog”_ over social media and/or email. And I might give away a
shoutout for you.

Besides, my blog is [open-sourced](https://github.com/Jarmos-san/blog)! ❤ So, if
you stumble across a roadblock, check out how I maintain mine. Or, feel free to
open an [Issue](https://github.com/Jarmos-san/blog/issues/new/choose)/
[Discussion](https://github.com/Jarmos-san/blog/discussions) thread.

That said, I look forward to how you use Hugo to share your amazing content with
the rest of the world!

_Originally published at_
[_https://jarmos.netlify.app_](https://jarmos.netlify.app/posts/blogging-with-hugo-as-an-wordpress-alternative/)
_on February 21, 2021._
