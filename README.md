# stephengfriend.github.io

A simple jekyll site for [stephengfriend.com](https://stephengfriend.com) based on [Pixyll](http://www.pixyll.com) by [John Otander](http://johnotander.com)

-----

## Getting Started

Ensure that Ruby is installed, `ruby --version`, and that the version is at least 1.9.3

### Installing Bundler

If you don't have Bundler already installed, you will need to go ahead and do that.

```
$ gem install bundler
```

### Install Dependencies

Bundler uses the Gemfile to determine what dependencies need to be installed. Let's install them now.

```
$ bundle install
```

#### Verify your Jekyll version

It's important to also check your version of Jekyll since this project uses Native Sass which
is [only supported by 2.0+](http://jekyllrb.com/news/2014/05/06/jekyll-turns-2-0-0/).

```
$ jekyll -v
# This should be jekyll 2.0.0 or later
```

### Clone the Repo

Grab a local clone of the repo.

```
$ git clone git@github.com:stephengfriend/stephengfriend.github.io.git
$ cd stephengfriend.github.io/
```

### Making Changes

Let's add some new content.

```
$ rake post['A New Post']
mkdir -p _posts
Creating new post: _posts/2015-12-04-a-new-post.md
$ $EDITOR _posts/2015-12-04-a-new-post.md
```

After, or during, making changes, you can preview your changes.

```
$ rake preview
```

Now you can navigate to `localhost:4000` in your browser to see the site.

## Upgrading Pixyll

Since this site is based on Pixyll, we'll want to stay up-to-date with any changes.

#### Ensure there's an upstream remote

If `git remote -v` doesn't have an upstream listed, you can do the following to add it:

```
git remote add upstream https://github.com/johnotander/pixyll.git
```

#### Pull in the latest changes

```
git pull upstream master
```

There may be merge conflicts, so be sure to fix the files that git lists if they occur. That's it!
