---
title: Giving this Jekyll thing a try
layout: post
---
Stumbling on the term [Jekyll][jekyll] again, this time on [GitHub][github], decided to see what it's about. It is said to be a simple tool to build and host static websites. It is built on Ruby, and among other features, supports markdown, and is blog-aware.

Installing Jekyll
-----------------
I set things up on [Ubuntu][ubuntu-desktop] Linux running on a [VirtualBox][virtualbox] in Windows 10. On this platform, [installation][jekyll-install] of Jekyll was straight-forward (less so on an [RPI3](#hid-rpi3)).

Creating a site
---------------

Created my first site from the [quickstart][jekyll-qs], which runs fine (ignoring the sass related deprecation warnings).

**_NOTE:_** By default, you won't be able to connect to your server through a browser on the VirtualBox's host machine. To solve this, I recommend creating a secondary host-only adapter, following [this approach][vb-connect]. Additionally, you'll need to add `--host 0.0.0.0` to your `jekyll serve` call to allow access from machines other than localhost.

Publishing on GitHub
--------------------

One of the interesting things about Jekyll is that [GitHub Pages][github-pages] supports Jekyll, building and serving your Jekyll site, for free. Followed [this tutorial][github-jekyll], and ignored step 6 for the time being, because I had nothing in the repo yet, there was no branch to choose from at this point.

Now I had already created the site without using the github-pages gem, and without using `--skip-bundle`. After I ran `bundle install` in step 12 of the tutorial, this resulted in:

```
Could not find gem 'http_parser.rb (= 0.8.0)' ...
```
This was caused by `jekyll new` adding to the `Gemfile`:
```
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
```
Commenting it out resolved the issue, but I decided to start from scratch and build following the tutorial instead. Testing it locally (step 14), resulted in:

```
servlet.rb:3:in `require': cannot load such file -- webrick (LoadError)
```

This appears to be a result of using an older version of Jekyll (imposed by using the github-pages gem) with a newer version of Ruby. A workaround was proposed [here][webrick], running `bundle add webrick`. The page runs OK locally now.

In step 17, using branch `master`, which seems to be the default branch when commit without specifying a branch name explicitly. At this point I also reviewed step 6, and noticed that GitHub had successfully set the deployment branch to `master`. In seconds, the site was hosted successfully on GitHub pages.

Changing the theme
------------------

The default site template uses the minima theme. I find it very elegant, but found the [jekyll-dash][dash-gh] theme by Bitbrain that I liked. It comes with instructions, but I thought I'd give this [GitHub tutorial][ghp-add-theme] a try that uses the remote_theme key in the `_config.yaml`. Adding `remote_theme: bitbrain/jekyll-dash` worked. This, however, doesn't work locally, and would require to work on difference branches for `_config.yaml`, so I moved back to the instructions by Bitbrain for installing it locally.

Using the github-pages gem set the jekyll version to 3.9.2, so I need to use jekyll-dash version 1. After finalizing the installation and configuration steps, the theme was showing locally, but the posts were missing and jekyll reported:

```
Pagination is enabled, but I couldn't find an index.html page...
```

Apparently, this theme requires an index.html, so I renamed index.markdown to index.html, which got rid of the message and made the posts show up again. In hindsight, this was mentioned in the FAQ for [jekyll-dash][dash-gh].

Publishing the theme
--------------------

At this point, regardless the warnings by Bitbrain, I thought I'd give it a swing and pushed the changes to GitHub and check the result. I received an email telling me the build process had failed and checked the details in the Actions tab:

```
The jekyll-dash theme could not be found.
```

As mentioned above, Bitbrain already warned that GitHub Pages generation process supports a limited list of plugins, which does not include jekyll-dash. While he refers to [documentation on GitHub actions][github-actions], I noticed the GitHub Actions option in Settings > Pages > Source of the repository, and decided to switch to it, and select Configure, which brings you to a file called `jekyll.yml`. I tried committing the default content, which triggered the action. This resulted in success.

Conclusions
-----------

While it wasn't all that hard to set up, it's not for everyone, it does require a technical mindset to set up and maintain.

On the RPI3 {#hid-rpi3}
-----------

I started off installing on my [RPI3][rpi3]. Installation was more painful there. On the Raspbian Buster distro, the latest version of Ruby was 2.5.0, which is not compatible with recent versions of Jekyll. And I wasn't about to upgrade my distro. Instead, I ended up following [this tutorial][rbenv-tutorial] to build Ruby using a tool called [rbenv][rbenv]. This worked nicely, but later I ran into various other limitations, and realized that the RPI was not the best platform to use generally as website development environment, and moved to Ubuntu.

[jekyll]: https://jekyllrb.com/
[github]: https://github.com/
[github-pages]: https://pages.github.com/
[github-jekyll]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll
[ubuntu-desktop]: https://ubuntu.com/download/desktop/
[virtualbox]: https://www.virtualbox.org/
[jekyll-install]: https://jekyllrb.com/docs/installation/ubuntu/
[rpi3]: https://www.raspberrypi.com/products/raspberry-pi-3-model-b/
[rbenv]: https://github.com/rbenv
[rbenv-tutorial]: https://www.theodinproject.com/lessons/ruby-installing-ruby
[vb-connect]: https://serverfault.com/a/333584
[dash-gh]: https://github.com/bitbrain/jekyll-dash
[jekyll-qs]: https://jekyllrb.com/docs/
[webrick]: https://github.com/jekyll/jekyll/issues/8523#issuecomment-751409319
[ghp-add-theme]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll
[github-actions]: https://jekyllrb.com/docs/continuous-integration/github-actions/