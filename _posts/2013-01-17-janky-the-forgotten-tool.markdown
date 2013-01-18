---
layout: post
title: Janky - The forgotten tool
tags: [janky, jenkins, github, ci, continuous, integration, campfire, hubot]
---

Where I work we use [GitHub][] as our Git central repository, and we have Yoda, our [Hubot][], helping us on our day to day tasks. We had to setup a Continuous Integration server for our project and after looking at all the CI options out there, we had to decide between [Travis-ci][] or [Jenkins][]. Both are well-known continuous integration server but they were not exactly what we needed at the moment. Travis is $150 per month and Jenkins has to be manually installed and configured, plus it has an awkward interface.

Travis could certainly be our solution in a future moment but, for now, we decided to try Jenkins. As a solution for the issues raised, we decided to use [Janky][], _"(...) a continuous integration server built on top of Jenkins, controlled by Hubot, and designed for GitHub"_.

When I first read Janky's readme, I thought it would be an easy setup... I couldn't be more **wrong**. There are too many environment variables you have to setup and apparently GitHub is not using the gem version they release on their own servers... Anyway, one can always fork and submit pull requests, right?

So, let me guide you through the setup process.

## Installing Jenkins

Janky recommends Jenkins version 1.427, but I have installed a newer version and I remember I had a problem but I managed to solve it – *I forgot the problem* –, so you don't need to worry about newer versions. [Installing Jenkins][] guide is easy to follow and will help you on installing a Jenkins server if you don't have one yet.

## Installing Janky

[Janky's guide][] on the other hand will guide you to **failure** <strike>since the beginning as the [Gist][] you use to install is outdated and lacks the *JANKY_BRANCH* variable definition.</strike>. Apparently the gist has been updated, so you can **almost** run the commands as they say:

{% highlight bash %}
➜ git clone git://gist.github.com/1497335 janky
➜ cd janky
➜ heroku create --stack cedar
{% endhighlight %}

At this point, I suggest to not use the latest **released gem** version (0.9.13) – as its **outdated** – and bundle the gem directly from the repository by changing the following lines in the Gemfile.

{% highlight ruby %}
gem "janky"
{% endhighlight %}

to:

{% highlight ruby %}
gem "janky", :git => "git://github.com/github/janky.git"
{% endhighlight %}

And now we can proceed with the installation guide:

{% highlight bash %}
➜ bundle install
➜ git add Gemfile.lock
➜ git commit Gemfile.lock -m "lock bundle"
➜ git push heroku master
{% endhighlight %}

## Configuring Janky

All variables are defined with the following syntax:
{% highlight bash %}
➜ heroku config:add VARIABLE=value
{% endhighlight %}
<br/>

Define janky's URL on heroku **with a trailing slash**:
{% highlight bash %}
JANKY_BASE_URL: http://JANKY_APP_NAME.herokuapp.com/
{% endhighlight %}

Our server has Jenkins' *Matrix-based security* enabled, so only registered users can access Jenkins. In order to authorize Janky, you need to create an account for it and specify both the username and its api token that can be found on the user configuration page: *http://jenkins.example.com/user/#{jenkins_user_name}/configure* on the URL.
{% highlight bash %}
JANKY_BUILDER_DEFAULT: http://USER:API_TOKEN@jenkins.example.com/
{% endhighlight %}

Define the configuration dir (default):

{% highlight bash %}
JANKY_CONFIG_DIR: /app/config
{% endhighlight %}

Hubot's user and password are the same passwords you will use when configuring Janky's hubot script. Create your own secure user/password here.

{% highlight bash %}
JANKY_HUBOT_USER: Login used to protect the Hubot API.
JANKY_HUBOT_PASSWORD: Password for the Hubot API.
{% endhighlight %}

You have to create a github account (or use your own) to allow janky to connect to github and set up service hooks for you.

{% highlight bash %}
JANKY_GITHUB_USER: Login of the GitHub user. Requires Administrative privileges to set up service hooks.
JANKY_GITHUB_PASSWORD: Password for the GitHub user.
{% endhighlight %}

There are two variables that apparently should be configured with the same value, and this token value is obtained by using the one that comes in the following curl command response:

{% highlight bash %}
➜ curl -u USERNAME:PASSWORD -d '{ "scopes": [ "repo:status" ], "note": "janky" }' \
https://api.github.com/authorizations

JANKY_GITHUB_HOOK_SECRET: Secret used to sign hook requests from GitHub.
JANKY_GITHUB_STATUS_TOKEN: To update pull requests with the build status.
{% endhighlight %}

You should also specifiy the default chat room, but I don't know whether this is the room name or number on campfire. It should not be a problem as long as you specify the room for each project.

{% highlight bash %}
JANKY_CHAT_DEFAULT_ROOM: Chat room where notifications are sent by default.
{% endhighlight %}

## Migrating the database

Now you can run the database migration.

{% highlight bash %}
➜ heroku run rake db:migrate
{% endhighlight %}

## Configuring hubot

Configuring hubot is pretty easy, all you have to do is enable its script on *hubot-scripts.json*:
{% highlight json %}
["janky.coffee"]
{% endhighlight %}

and define an environment variable on hubot's server (not janky's). Don't forget to append **_hubot/** to the URL:

{% highlight bash %}
HUBOT_JANKY_URL: http://JANKY_HUBOT_USER:JANKY_HUBOT_PASSWORD@JANKY_APP_NAME.herokuapp.com/_hubot/
{% endhighlight %}

## Troubleshooting

After all heroku commands you might need to specify the app on which you want to run the command using the *--app* argument.

{% highlight bash %}
➜ heroku run rake db:migrate --app MY_APP_NAME
{% endhighlight %}


[Gist]: https://gist.github.com/1497335
[GitHub]: https://www.github.com
[Hubot]: http://hubot.github.com
[Janky]: https://github.com/github/janky
[Janky's guide]: https://github.com/github/janky#deploying
[Jenkins]: http://jenkins-ci.org/
[Installing Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins
[Travis-ci]: https://travis-ci.org/
