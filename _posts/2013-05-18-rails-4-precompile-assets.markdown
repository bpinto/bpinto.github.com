---
layout: post
title: Rails 4 precompile assets
tags: [rails, sprockets, assets]
---

Adding `config.assets.precompile` to your config/environments/production.rb file is not working on rails 4. This [bug][bug] has been raised on sprocket-rails and as a temporary fix, move this setting to config/application.rb

{% highlight ruby %}
class Application < Rails::Application
  ...

  # Precompile additional assets.
  # application.js, application.css, and all non-JS/CSS in app/assets folder are already added.
  config.assets.precompile += %w( head.js )
end
{% endhighlight %}


[bug]: https://github.com/rails/sprockets-rails/pull/36
