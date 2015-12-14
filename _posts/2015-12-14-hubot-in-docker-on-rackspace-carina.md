---
layout:     post
title:      Hubot in Docker on Rackspace Carina
date:       2015-12-14 18:05
summary:    Launching a Hubot instance on Carina is as easy as `docker run`
categories: docker hubot carina gitter
---

Rackspace recently launched [Carina](https://getcarina.com), their container environment service based on docker swarm. I've
been wanting to put together a container based continuous delivery workflow to demo for a client and
Carina seems like a perfect place to do it. To help me understand how Carina easily enables launching
docker containers, I decided to launch a [Hubot](https://hubot.github.com/) instance on their service.

If you'd like to dive right in, head over to the [source code](https://github.com/stephengfriend/carinabot) and fork it. You can also interact with Carinabot over at [Gitter.im](https://gitter.im/stephengfriend/carinabot)

_Note: This article assumes Mac OSX but should translate to any linux platform with little modification_

#### Prerequisites

- [docker](https://docs.docker.com/engine/installation/mac/)
- [carina cli](https://getcarina.com/docs/getting-started/getting-started-carina-cli/)
- [node.js and npm](http://blog.teamtreehouse.com/install-node-js-npm-mac)
- [Github](https://github.com) account
- [gitter.im](https://gitter.im) account

### Launch a Carina Cluster

The carina cli[^1] allows us to create and destroy our clusters, download credentials
and prepare our environment.

{% highlight sh lineanchors %}
# Expose our login credentials for carina
export CARINA_USERNAME=<your username>
export CARINA_APIKEY=<your apikey>

# Create a cluster (this may take some time to finish)
carina create --nodes=1 --autoscale --wait Carinabot

# Download credentials and prepare the environment
carina credentials Carinabot && eval $(carina env Carinabot)

# Confirm the environment is up
docker info
{% endhighlight %}

### Assembling Carinabot

Now we can assemble Carinabot, an instance of Hubot[^2].

{% highlight sh lineanchors %}
# Install Dependencies
npm install -g yo generator-hubot

# Make a directory for our bot and generate the boilerplate.
mkdir carinabot && cd carinabot

# Assemble Carinabot
yo hubot                                                            \
    --name="carinabot"                                              \
    --owner="Bot Wrangler <bw@example.com>"                         \
    --description="A starry eyed robot running on a carina cluster" \
    --adapter=gitter2                                               \
    --defaults
{% endhighlight %}

Optional, but recommended:

{% highlight sh lineanchors %}
# Create a git repo and commit our newly assembled Carinabot
git init . && git add .
git commit -m "Initial Assembly"
{% endhighlight %}

We can, and should, test that Carinabot is working as expected.

{% highlight sh lineanchors %}
bin/hubot

# Now type carinabot help at the `carinabot>` prompt
carinabot> carinabot help
{% endhighlight %}

### Carinabot as a Container

What's a better welcoming gift than stuffing our bot into a tiny little container? Let's
create a `Dockerfile` to define how this container should be built.

{% highlight docker lineanchors %}
# For simplicity, we will use the public node image
FROM node
MAINTAINER Bot Wrangler, bw@example.com
ENV BOTDIR /opt/bot

RUN mkdir ${BOTDIR}

# These step allows us to cache our node_modules
COPY package.json ${BOTDIR}
RUN cd ${BOTDIR} && npm install
COPY . ${BOTDIR}

WORKDIR ${BOTDIR}

CMD bin/hubot
{% endhighlight %}

With our `Dockerfile` defined, we can now build an image from it.

{% highlight sh lineanchors %}
docker build -t carinabot .
{% endhighlight %}

### Intermission

Before we introduce Carinabot to the world, there are a few details to note. We will be using [Gitter.im](https://gitter.im/) and the [gitter2 adapter](https://github.com/huafu/hubot-gitter2). Gitter's business model is tightly coupled to [GitHub](https://github.com/). As such, you will need a GitHub account to [sign up for Gitter](https://gitter.im/login/github?action=signup&source=intro-signup).

The gitter2 adapter uses the Gitter API and requires a [Personal Access Token](https://developer.gitter.im/apps) to authenticate with the endpoint. We will pass this token via an environment variable when we run our docker container. Additionally, the bot will not respond to commands sent from the account it is logged in as, so you will need a second Gitter.im account to communicate with the bot.

### Carinabot's Grand Debut

Let's resume the show!

Now that we've installed our tools, assembled Carinabot, packaged her into a portable container image, and got our accounts created,
we're ready to flip the switch and debut our creation.

{% highlight sh lineanchors %}
docker run -e "HUBOT_GITTER2_TOKEN=<Personal Access Token>" \
           -d --name carinabot carinabot ./bin/hubot -a gitter2

docker ps # should return something similar to the following:
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
ad323881db32        carinabot           "./bin/hubot -a gitte"   7 seconds ago       Up 7 seconds                            b5fdd925-ced4-4778-a8bf-b5dd54bffbf9-n1/carinabot
{% endhighlight %}

Congratulations! We've got a working Hubot container running.

Head on over to Gitter.im and send a direct message to the account name your bot is running as to see it in work.

![carinabot communications](http://dpr.stephengfriend.com/1bpG7.png)

Next time, we'll add a Redis Brain to Carinabot and introduce [Docker Compose](https://docs.docker.com/compose/) for orchestrating our containers.

---

[^1]: For an explanation of all capabilities and available options, check out the [carina cli documentation](https://github.com/getcarina/carina)
[^2]: Explaining Hubot in depth is outside the scope of this post. If you'd like, head on over to the [Hubot documentation](https://hubot.github.com/docs/) to familiarize yourself.
