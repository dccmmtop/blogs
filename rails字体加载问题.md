## rails字体加载问题

[原文连接](https://gist.github.com/anotheruiguy/7379570)

Web fonts are pretty much all the rage. Using a CDN for font libraries, like TypeKit or Google Fonts, will be a great solution for many projects. For others, this is not an option. Especially when you are creating a custom icon library for your project.

Rails and the asset pipeline are great tools, but Rails has yet to get caught up in the custom web font craze.

As with all things Rails, there is more then one way to skin this cat. There is the recommended way, and then there are the other ways.

## The recommended way

Here I will show how to update your Rails project so that you can use the asset pipeline appropriately and resource your files using the common Rails convention.

**The default asset pipeline**

When looking in your project's `assets` directory, you would see the following:

```
|-app/
|---assets/
|-----images/
|-----javascripts/
|-----stylesheets/
```

What we need to do is add the `fonts` directory within the `assets` directory so that we can resource these files in our `CSS`or `Sass` files using proper rails conventions and the asset pipeline.

The problem is, simply adding a `fonts` directory isn't picked up by the pipeline. For now, let's just add that in there and then fix the Rails issue next.

```
|-app/
|---assets/
|-----fonts/
|-----images/
|-----javascripts/
|-----stylesheets/
```

**Updating the asset pipeline**

The fix is pretty simple. Open your project's config file, located at `config/application.rb` and add the following line within your `Application` class:

```
config.assets.paths << Rails.root.join("app", "assets", "fonts")
```

BOOM! Now Rails is smart enough to know what to do with assets within the `fonts` directory.

**Fonts path in your Sass**

Default CSS that you will get from sites like [icomoon.io](http://icomoon.io/) will typically look something like this:

```
@font-face {
	font-family: 'icofonts';
	src:url('fonts/icofonts.eot');
	src:url('fonts/icofonts.eot?#iefix') format('embedded-opentype'),
		url('fonts/icofonts.ttf') format('truetype'),
		url('fonts/icofonts.woff') format('woff'),
		url('fonts/icofonts.svg#icofonts') format('svg');
	font-weight: normal;
	font-style: normal;
}	
```

That seems right, unless you are using the asset pipeline. So to make the path correct we need to make a slight update and replace `src:url()` with `src:font-url()`. Our Sass file would look like:

```
@font-face {
  font-family:'icofonts';
  src:font-url('icofonts.eot');
  src:font-url('icofonts.eot?#iefix') format('embedded-opentype'),
  
  ...
} 
```

When the Sass is rendered into CSS, you should see something like the following:

```
@font-face {
  font-family: 'icofonts';
  src: url(/assets/icofonts.eot);
  src: url(/assets/icofonts.eot?#iefix) format("embedded-opentype"), 
    url(/assets/icofonts.ttf) format("truetype"), 
    url(/assets/icofonts.woff) format("woff"), 
    url(/assets/icofonts.svg#icofonts) format("svg");
  font-weight: normal;
  font-style: normal;
}
```

Perfect. Our fonts are in the `assets` directory where it feels best. Our code is clean and follows all the common Rails conventions. This is the best possible soliton and if you are happy here, do not read the rest of this article. It get's a little weird from here on.

## The way of the hacker

Anything you put into a directory within the `assets` directory will be carried through the pipeline. So, why not put the `fonts`directory within the `stylesheets` directory?

If you are like me, that just feels weird, but I don't judge. If you don't want to or can't create the `fonts` directory within the `assets` directory or can't update the `application.rb` file, you could do something like the following:

```
|-app/
|---assets/
|-----images/
|-----javascripts/
|-----stylesheets/
|-------fonts/
```

**The Sass**

This is kind of interesting. Since the `fonts` directory is in the `stylesheets` directory, the fonts just come along for the ride. Simply use `(asset-path(' ... '))` so that Rails creates the proper path.

```
@font-face {
  font-family:'icofonts';
  src:url(asset-path('fonts/icofonts.eot'));
  src:url(asset-path('fonts/icofonts.eot?#iefix')) format('embedded-opentype'),
    
  ...
}
```

When the Sass is processed into CSS, you should see the following:

```
@font-face {
  font-family:'icofonts';
  src: url('/assets/fonts/icofonts.eot');
  src: url('/assets/fonts/icofonts.eot?#iefix') format("embedded-opentype"),
  
  ...
}
```

**Hardcoding the path, it works, but ... yuk**

What's interesting about this is, since you are putting the `fonts` directory inside the `stylesheets` directory, do you really need to use the `src:url(asset-path('fonts/icofonts.eot'));` method? At this point the fonts are already in the asset pipeline and the following code, although not ideal, hardcoding `/assets/` into the path will work.

```
@font-face {
  font-family:'icofonts';
  src:url('/assets/fonts/icofonts.eot');
  src:url('/assets/fonts/icofonts.eot?#iefix') format("embedded-opentype"),
  ...
}
```

But ... since the `fonts` directory is within the `stylesheets` directory and this is already in the asset pipeline, if you just refer to the fonts in a relative path, that works too. But man, that's just doesn't feel very Rails to me.

```
@font-face {
  font-family:'icofonts';
  src:url('fonts/icofonts.eot');
  src:url('fonts/icofonts.eot?#iefix') format("embedded-opentype"),
  ...
}
```

**But ... I'm not using Sass?**

Ok, first I have to ask ... WAT? Ok fine, not using Sass, going with good 'ol CSS. Interesting enough, putting the `fonts`directory is within the `stylesheets` directory again works and you can simply use the relative path.

```
@font-face {
  font-family: 'icofonts';
  src:url('fonts/icofonts.eot');
  src:url('fonts/icofonts.eot?#iefix') format("embedded-opentype"),
  ...
}
```

We all know that this kind of sucks. So if we wanted to bring the asset pipeline back into play we can't use CSS by itself, we need to add some Ruby juice to this. But how? Yup, make the file `name.css.erb`, yeah, I said that.

Ok, so for this example we need to get straight up Ruby on this. The `('fonts/icofonts.eot')` needs to be wrapped in some Ruby `<%= asset_path('...') %>;`

```
@font-face {
  font-family: 'icofonts';
  src:url('<%= asset_path('fonts/icofonts.eot') %>');
  src:url('<%= asset_path('fonts/icofonts.eot?#iefix') %>') format('embedded-opentype'),
  ...
}
```

This is very similar to the earlier syntax of `src:url(asset-path('fonts/ ... '));` I illustrated earlier, but WOW! `css.erb`and `'<%= ... %>'` just causes me a little pain.

You can eliminate this by simply changing any `.css` file to a `.css.scss` file and then take full advantage of all the awesome that Sass brings you and not hack Ruby onto plain CSS. Just my $0.02.

## In conclusion

Although I am illustrating seemingly clever ways to solve this problem, in the end the best solution really is to update your Rails project and use the asset pipeline as intended. And if you aren't using Sass yet in your project and want to learn, I strongly suggest looking at [Sass 101 - A Newb's Guide](https://speakerdeck.com/anotheruiguy/sass-101-a-newbs-guide).

I should also note that many of these hacked techniques I did not test in a development environment with cache busting. Use at your own risk.