+++
date = "2017-03-20T16:11:58+05:00"
draft = false
title = "Updating the foundation-themes project for Foundation v3.6.1"
tags = [ "Foundation", "UI" ]
categories = [ "Design" ]
image = "updating-the-foundation-themes-project-for-foundation-v361.png"
+++

# Overview

This post covers the process I went through to update the [SD UI Foundation Themes project](https://github.com/es-di/foundation-themes) to work with the newer [6.3.1 release of Foundation Sites](https://github.com/zurb/foundation-sites/tree/6.3.1). It covers cloning the source, stepping through initial build errors, running a checklist through the Foundation Sites migration notes, and wrapping up with some quality control checks before we call it a day. Upgrading the Classic theme is covered in this article, but the same steps will work for the dark, gradient, and/or advanced-bootstrap themes as well.

## Foundation Sites SASS files

Foundation Sites has a convention that you'll have an app.css and _settings.css to manage what gets pulled into your final CSS file(s) and also what styles actually get used to make things look how you would like. In our case, we'll be focused mainly on _settings.css since this post isn't covering what to include / exclude from your ultimate SASS files.

## Foundation Sites Release Notes

It's important to look at any Migration notes for non-RC releases since v6.2.3 and understand what has changed over that time. If we don't cover all the bases in this process, we may miss something important, which will most likely cause confusion and improper implemention going forward (e.g. you might write your own styles to compensate for something that actually got implemented in a prior release, meaning you will no longer gain from maintenance done by the vendor or possibly be able to take advantage of new features down the road without re-working a lot of things).

The Foundation Sites release notes can be found on GitHub at the following location: https://github.com/zurb/foundation-sites/releases. I've included them below so you don't have to go digging through it all.

### Migration Notes

{{< tabs "migration-notes" >}}
    {{< tabs-title title="v624" isActive="true" >}}
    {{< tabs-title title="v630" >}}
    {{< tabs-title title="v631" >}}
{{< /tabs >}}

{{< tabs-content "migration-notes" >}}

    {{% tabs-panel title="v624" isActive="true" %}}

* The default setting for Equalizer on small (stacking) has been changed to not equalize when items are stacked. If you wish to revert to the previous behavior, simply add data-equalize-on-stack="true" to your equalizer components.

    {{% /tabs-panel %}}

    {{% tabs-panel title="v630" %}}

* If you're using the ZURB template, check out this pull request to see the changes you'll want to make to update: https://github.com/zurb/foundation-zurb-template/pull/49/files
* Flex Videos are now Responsive Embeds. Old classes and mixin names are now deprecated. Check out #8765
* Accordion Items now MUST be labeled with data-accordion-item, they are no longer inferred from li elements. Check out #9178
* The dist directory structure has changed. CSS files now live in dist/css, JS files in dist/js. Check out #9078
* The default color palette has changed to be meet AA contrast requirements. This has changed both defaults and a number scss variables and functions. Check out #9319 (comment)
* In order to facilitate vertical rhythm layouts, the old $header-sizes map has been replaced with a more general $header-styles map. $header-styles map not only allows to set  the font-size, but also line-height, margin-top and margin-bottom per header size and breakpoint. $header-sizes still works, however, it is going to be depreciated. If $header-sizes is present in your _settings.scss it overrides any $header-styles map. Check out #9419
* Some older flex based mixins (flex-grid-order, flex-grid-row-align, flex-grid-column-align) and classes (.align-column/.align-row), that had been deprecated are now removed, along with a few old variables that are no longer relevant. Check out #9437

    {{% /tabs-panel %}}

    {{% tabs-panel title="v631" %}}

None

    {{% /tabs-panel %}}

{{< /tabs-content >}}

We can see from the 6.2.4 Migration Notes, you only need to worry if you're using the equalizer component in your own project. Since this doesn't affect the Foundation Themes project, we can safely ignore this.

If we skip-ahead real quick to the 6.3.1 tab, you'll see there are literally no migration notes to worry about with that release.

That leaves us with the migration notes for 6.3.0. This is the section we'll refer to throughout this article for the changes that a required to pull Foundation Themes forward to match Foundation Sites 6.3.1. Before we dive in, let's get a working directory setup to hack and play around with things throughout this process.

## Acquire Source

I highly recommend cloning Foundation Themes into a new directory, and to not attempt editing for a pre-existing version you may be using in a project already. It can get extremely confusing switching back-and-forth between versions as-is, so having a full project on-top during this process is just asking for trouble. I stored my clone in a \Source\ directory on my data drive.

### Clone Foundation Themes Repository

``` text
git clone https://github.com/es-di/foundation-themes.git
cd foundation-themes
```

### Update Foundation Sites version to v6.3.x

Open up the package.json file and change foundation-sites to pull the more recent 6.3.x version. At the time of this writing, you will be updating to 6.3.1.

```
"foundation-sites": "~6.3.0"
```

### Install npm packages (including updated foundation-sites)
``` text
npm install
```


## Fix Compilation / Runtime errors

Before addressing the list of migration notes in its entirety, I felt like going down the path of getting the compile / runtime errors to go away first, so that I at least had something running and to work against for the rest of the items.

### Run default npm script

According to the README.md and looking in the package.json file, we can see that we want run the 'start' npm script to start a development server mode. This starts us off with the Classic theme in context. Don't be surprised, but a first run of this isn't going to happen since things will be broken.

```
npm run
```

### $header-sizes Deprecation

If we check the migration notes, we can see that there was indeed an item related to $header-sizes and $header-styles. It states that "the old $header-sizes map has been replaced with a more general $header-styles map. $header-styles map not only allows to set the font-size, but also line-height, margin-top and margin-bottom per header size and breakpoint. $header-sizes still works, however, it is going to be depreciated. If $header-sizes is present in your _settings.scss it overrides any $header-styles map"

Since we know we're upgrading to a newer version of the underlying Foundation Sites framework, we need to make sure we pay close attention to the output of starting the development server for the first time. If we look at the output in this case, you can instantly see that we have an issue, before any of the SASS is even being stitched together.

```
WARNING: Note, that $header-sizes has been replaced with $header-styles. $header-sizes still works, but it is going to be depreciated.
Backtrace:
        node_modules/foundation-sites/scss/typography/_base.scss:77, in function `build_from_header-sizes`
        node_modules/foundation-sites/scss/typography/_base.scss:91
```

The developers of Foundation Sites have been nice enough to put an @warn statement in their SASS, so that we're alerted to the fact that we're using deprecated functionality that might possibly be removed down the road.


If we search for the $header-sizes function in the theme, we find that it lives in source/scss/_settings.scss. Next, we search for $header-styles in the foundation-sites source to figure out what that new definition is, and how we can update our current theme to use it.


{{< tabs "header-styles-update" >}}
    {{< tabs-title title="_settingsscss" isActive="true" >}}
    {{< tabs-title title="_basescss" >}}
    {{< tabs-title title="Migration Notes" >}}
{{< /tabs >}}

{{< tabs-content "header-styles-update" >}}

    {{% tabs-panel title="_settingsscss" isActive="true" %}}

```
$header-sizes: (
  small: (
    'h1': 24,
    'h2': 20,
    'h3': 19,
    'h4': 18,
    'h5': 17,
    'h6': 16,
  ),
  medium: (
    'h1': 48,
    'h2': 40,
    'h3': 31,
    'h4': 25,
    'h5': 20,
    'h6': 16,
  ),
);
```

    {{% /tabs-panel %}}

    {{% tabs-panel title="_basescss" %}}

```
$header-styles: (
  small: (
    'h1': ('font-size': 24),
    'h2': ('font-size': 20),
    'h3': ('font-size': 19),
    'h4': ('font-size': 18),
    'h5': ('font-size': 17),
    'h6': ('font-size': 16),
  ),
  medium: (
    'h1': ('font-size': 48),
    'h2': ('font-size': 40),
    'h3': ('font-size': 31),
    'h4': ('font-size': 25),
    'h5': ('font-size': 20),
    'h6': ('font-size': 16),
  ),
) !default;
```
        
    {{% /tabs-panel %}}

    {{< tabs-panel title="Migration Notes" >}}

      In order to facilitate vertical rhythm layouts, the old $header-sizes map has been replaced with a more general $header-styles map. $header-styles map not only allows to set the font-size, but also line-height, margin-top and margin-bottom per header size and breakpoint. $header-sizes still works, however, it is going to be depreciated. If $header-sizes is present in your _settings.scss it overrides any $header-styles map. Check out #9419

    {{< /tabs-panel >}}

{{< /tabs-content >}}



In our case, we find that really the only changes we need to make are defining our header classes a little more detailed.

Since the _base.scss file holds the same header sizes and styling as the Classic theme, and is using the !default flag, and isn't referenced elsewhere in _settigs.scss (todo: prove if this matters, setup test case)...we can literally just remove the entire $header-sizes variable from _settings.scss and let the Foundation Sites _base.scss file handle setting those up for us...nice, I mean, who doesn't like removing redundant crap?

### See if we fixed the issue

Let's now re-run the default npm script for this project and see if we got rid of the $header-sizes warning...

```
npm run start
```

Hooray! The warning is gone! (at least it should be if you removed the correct portions)

### Onto the next one...

Unfortunately, we now have more errors to deal with. Don't get discouraged though, remember, we're revving Foundation Themes across multiple versions of Foundation Sites, so we're bound to hit multiple issues to resolve. Next up is the error...

```
ERROR in ./classic/source/index.js
Module not found: Error: Cannot resolve 'file' or 'directory' D:\temp\foundation-themes\node_modules\foundation-sites/dist/foundation.min.js in D:\temp\foundation-themes\classic\source
 @ ./classic/source/index.js 4:0-44
```

If we check the migration notes again, we can see that there's an item stating "The dist directory structure has changed. CSS files now live in dist/css, JS files in dist/js". Let's go take a look at the foundation Sites directory and see what things are looking like...

<insert picture here>

Quickly you can tell that foundation.min.js now resides in /dist/js and not just /dist, just as the migration notes said. Let's update our index.js file in the Classic theme to reflect this.

{{< tabs "header-styles-update" >}}
    {{< tabs-title title="Old indexjs" isActive="true" >}}
    {{< tabs-title title="New indexjs" >}}
{{< /tabs >}}

{{< tabs-content "header-styles-update" >}}

    {{% tabs-panel title="Old indexjs" isActive="true" %}}

require('foundation/dist/foundation.min.js');

    {{% /tabs-panel %}}

    {{% tabs-panel title="New indexjs" %}}

require('foundation/dist/js/foundation.min.js');

    {{% /tabs-panel %}}

{{< /tabs-content >}}


### Verify we fixed the issue

Let's now re-run the default npm script for this project and see if we got rid of the missing foundation.min.js issue...

```
npm run start
```

## Argument `$color` of `red($color)` must be a color

We can see from the error here, that something in foundation-badge is causing issues (foundation-everything just includes ALL Foundation Sites components). We'll assume in our case (since we just did a fresh pull) that the foundation-sites folder contains the correct source for foundation-sites, so we should assume that's not the case. (Note: If you really want to find out, venture into the node_modules/foundation-sites folder, checkout tag 631, and run the npm scripts to build it yourself to check)

This leaves us to look at our _settings.scss file and see what we're doing with Badge.

```
ERROR in ./~/css-loader?sourceMap!./~/postcss-loader!./~/sass-loader?precision=8!./classic/source/scss/app.scss
Module build failed:
undefined
        ^
      Argument `$color` of `red($color)` must be a color

Backtrace:
        node_modules/foundation-sites/scss/util/_color.scss:19, in function `red`
        node_modules/foundation-sites/scss/util/_color.scss:19, in function `color-luminance`
        node_modules/foundation-sites/scss/util/_color.scss:44, in function `color-contrast`
        node_modules/foundation-sites/scss/util/_color.scss:63, in function `color-pick-contrast`
        node_modules/foundation-sites/scss/components/_badge.scss:59, in mixin `foundation-badge`
        node_modules/foundation-sites/scss/foundation.scss:84, in mixin `foundation-everything`
        stdin:5
      in D:\temp\foundation-themes\node_modules\foundation-sites\scss\util\_color.scss (line 19, column 10)
 @ ./classic/source/scss/app.scss 4:14-190
```

```
$badge-background: $primary-color;
$badge-color: foreground($badge-background);
$badge-padding: 0.3em;
$badge-minwidth: 2.1em;
$badge-font-size: 0.6rem;
```

If we go back and take a look at the release notes, we'll see an item related to colors..."The default color palette has changed to be meet AA contrast requirements. This has changed both defaults and a number scss variables and functions. Check out #9319 (comment)"

If we visit [this comment](https://github.com/zurb/foundation-sites/pull/9319#issuecomment-263414697), we can see that the ```foreground()``` function is no longer in-use and has been replaced by ```color-pick-contrast()```

Sure enough, $badge-color is using the, now defunct, foreground() method. Let's replace this with the color-pick-contrast() method.

### Does it compile now?

```
npm run start
```

Yay! It can successfully compile now!

This doesn't mean we're done however, we need to cover all of the migration notes and make sure we cover all of our bases.

## Migration Note #2


Flex Videos are now Responsive Embeds. Old classes and mixin names are now deprecated. Check out #8765

The .flex-video class and flex-video() mixin will be deprecated in a future version (6.4). They have been replaced by the .responsive-embed class and responsive-embed() mixin. This is a semantic improvement, as embedded content does not have to be a video.

### Remove flex-video

Remove the following from the _settings.scss for the Classic theme...

```
//  18. Flex Video
```

```
// 18. Flex Video
// --------------

$flexvideo-padding-top: rem-calc(25);
$flexvideo-margin-bottom: rem-calc(16);
$flexvideo-ratio: 4 by 3;
$flexvideo-ratio-widescreen: 16 by 9;
```

### Add responsive-embed




## Top Comments and util

First let us shore-up the comments at the top of _settings.scss file to make sure they match the latest in node_modules/foundation-sites/scss/settings/_settings.scss. This will help us ensure we cover every component in Foundation Sites 6.3.1 and have them in the same order to make upgrades easier down the road.

## Global

