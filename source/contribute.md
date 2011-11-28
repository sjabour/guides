## Contribute

The SproutCore documentation team welcomes any and all help from the community. If you are interested in contributing to the effort, the information below will let you know how to do so and who to contact in order to get help and guidance.

endprologue.

### How to Contribute?

First, you will need to fork the SproutCore guides repository from Github.

Go to `https://github.com/sproutcore/sproutguides` and fork it. Then, check your forked copy out onto your local machine.

<shell>
$ git clone git@github.com:<yourname>/sproutguides.git
</shell>
Switch to your newly cloned repository.

<shell>
$ cd sproutguides
</shell>

You will want to add the main SproutCore repository as a remote:

<shell>
$ git remote add upstream git://github.com/sproutcore/sproutguides.git
</shell>

For each guide you write, you will want to create a new branch:

<shell>
$ git fetch upstream
$ git checkout -b <branchName> upstream/master
</shell>

TIP: The -b switch is the branch name you are creating. When the time comes to push your changes back to the remote server you will need this name. Everyone will see this name, so choose wisely.

This will create a new branch for you to work on your new guide or to edit an existing guide. Next, you will need to get the `guides` executable.

#### If You Use OSX or Windows and Aren't a Ruby Developer

The easiest way to get started with the guides is to download and install the [guides pkg](http://guides-pkg.strobeapp.com/Guides.pkg) on OSX or the [guides exe](http://guides-pkg.strobeapp.com/Guides.exe) on Windows. This will give you the `guides` executable on your machine, and does not require you to have a C compiler on your computer.

#### If You're a Ruby Developer

h5. Normal gem install

To install the guides gem, just use:

<shell>
$ gem install guides
</shell>

h5. Installing with bundler

Alternatively you can use the normal bundler workflow to develop the guides.
This has the benefit of making sure you are using the correct version of the guides gem for development.

To install with bundler:

<shell>
$ gem install bundler
$ cd sproutguides
$ bundle install --binstubs
</shell>

The above commands will download the source, compile and install the `guides` executable in the `bin` sub-directory.

TIP: This action may take a while to download and place everithing in place for local usage.

For the rest of this guide, when the `guides` command is referenced, prefix it with `bin`. For instance, if you are instructed to run `guides preview`, instead run `bin/guides preview`.

### Create Your Guide

Create a new guide `<your_guide_name>.md` in the `source` directory.  Use the following template:

<plain filename="source/&lt;your_guide_name&gt;.md">
## Guide Topic

This guide covers ... After reading this guide, you will be able to:

 * Make a list of the things you will cover.
 * This should be a relatively high level list.
 * It should cover what the reader will know how to do once done.

endprologue.

### Topics Should Use `###`

Each topic should use an ###
</plain>

To preview your guide as you write it, run the `guides preview` command.

<shell>
$ guides preview
>> Thin web server (v1.2.7 codename No Hup)
>> Maximum connections set to 1024
>> Listening on 0.0.0.0:9292, CTRL+C to stop
</shell>

Go to `http://localhost:9292/<your_guide_name>.html` in a browser. After making a change, refresh to see it update live.

If you do not see your changes, check the terminal window where you ran the `preview` command for error messages.

#### Updating `guides.yml`

You will also want to update the main `guides.yml` in the root of the repository with your new guide. First, decide which section your guide will go under. In general, new guides will go under the `Digging Deeper` section.

Next, create a new entry for your guide. You will need to decide on a title and description. For the URL, enter the filename you used for your guide, without the `.md` ending. For example, if `<your_guide_name>` happens to be `weebles`, then your entry will look something like this:

<plain filename="guides.yml">
index:
  Digging Deeper:
    - title: Using SproutCore Weebles
      url: weebles
      text: "Learn how to use the SproutCore Weebles framework to build magical and revolutionary SproutCore applications"
</plain>

Once you are done, commit your work and then push your guide to your fork:

To commit all your changes:
<shell>
$ git commit -a
</shell>

Pushing your changes back to the remote repository:
<shell>
$ git push origin <branchName>
</shell>

TIP:  branchName above is the name you used at the beginning of the guide in the git checkout step.  If you can't remember the name use 'git branch -a' to list the branches.

Next, submit a pull request from your repository. Make sure to make the pull request using the branch you used for the guide.

### Some Guidelines

 * Guides are written in a modified Textile. For examples take a look at existing guides, or some of the [Rails Guides](https://github.com/rails/rails/tree/master/railties/guides/source) for some examples.
 * For now, follow the [Rails Guides Conventions](https://github.com/lifo/docrails/wiki/rails-guides-conventions).
 * Assets are stored in the `assets` directory.
 * Sample format: [Fixtures](http://github.com/sproutcore/sproutguides/blob/master/source/getting_started.md).
 * Sample output: [Fixtures](http://guides.sproutcore20.com/getting_started.html).

### What to Contribute?

 * We need authors, editors, proofreaders, and translators. Adding a single paragraph of quality content to a guide is a good way to get started.
 * The easiest way to start is by improving an existing guide:
 ** Improve the structure to make it more coherent.
 ** Add missing information.
 ** Correct any factual errors.
 ** Fix typos or improve style.
 ** Bring it up to date with the latest release of SproutCore.
 * We're also open to suggestions for entire new guides:
 ** Contact the documentation team to get your idea approved. See the Contact section below.

### What is the process?

 * Follow the instructions above to fork the repository and submit a pull request.
 * If you are a regular committer we will give you direct commit access.

### Rules

 * Guides are licensed under a Creative Commons Attribution-Share Alike 3.0 License.
 * If you're not sure whether a guide is actively being worked on, stop by IRC (information below) and ask.

### Mailing List

Documentation requests and questions can be posted on the [SproutCore mailing list](http://groups.google.com/group/sproutcore-dev)

### Contact

 * IRC: [#sproutcore-dev](irc://irc.freenode.net/sproutcore-dev)
 * Twitter: [@sproutcore](http://twitter.com/#!/sproutcore)
 * Email: TBD