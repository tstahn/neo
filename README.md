# neo

**Extensible, scalable, Sass-based, OOCSS framework for large and long-lasting
UI projects.**

neo is a framework in its truest sense: it does not provide you with UI and
design out of the box, instead, it provides you with a solid architectural
baseline upon which to complete your own work.

This project is a fork of [inuitcss](https://github.com/inuitcss/inuitcss) created by [Harry Roberts](https://csswizardry.com/).

## Installation

You can use neo in your project by installing it using a package manager
(recommended):

**npm:**

```
$ npm install neo --save
```

**yarn:**

```
$ yarn add neo
```

**Copy/paste (not recommended):**

You can download neo and save it into your project’s `css/` directory. This
method is not recommended because you lose the ability to easily and quickly
manage and update neo as a dependency.

## Getting Started

Once you have got neo into your project using one of the methods outlined
above, there are a handful of things we need to do before we’re ready to go.

Firstly, we need to identify any files whose name contain the word `example`.
These files are demo and/or scaffolding files that neo requires, but that
you are encouraged to create and define yourself. These files, as of v7.0.0,
are:

```
example.main.scss
components/_example.buttons.scss
```

Here’s what we need to do with them:

### [`example.main.scss`](https://github.com/tstahn/neo/blob/master/example.main.scss)

This is your main, or _manifest_, file. This is the backbone of any neo
project, and it is responsible for `@use`ing all other files. This is the
file that we compile out into a corresponding CSS file.

You need to copy this file from the directory that your package manager
installed it into, and move it to the root of your `css/` directory. Once there,
rename it `main.scss`.

Next, you’ll need to update all of the `@use`s in that file to point at the
new locations of each partial (that will depend on how your project is set up).

Once you’ve done this, you should be able to run the following command on that
file and get a compiled stylesheet without any errors:

```
path/to/css/$ node_modules/.bin/sass main.scss:main.css
```

**N.B.** If you downloaded neo, you do not need to move this file; you
can simply rename it.

### [`_example.buttons.scss`](https://github.com/tstahn/neo/blob/master/components/_example.buttons.scss)

You don’t need to really do much with this file other than ensure you don’t let
it into your final project!

This file exists to show you how you might build components into a neo
project, because components are the one thing that neo purposefully refuses
to provide.

You can, if you wish, copy this file to your own `css/components/` directory and
rename it `_buttons.scss`. You can now use this file as the basis for
your own buttons component.

## CSS directory structure

neo follows a specific folder structure, which you should follow as well in your own CSS directory:

* `/settings`: Global variables, site-wide settings, config switches, etc.
* `/tools`: Site-wide mixins and functions.
* `/generic`: Low-specificity, far-reaching rulesets (e.g. resets).
* `/elements`: Unclassed HTML elements (e.g. `a {}`, `blockquote {}`, `address {}`).
* `/objects`: Objects, abstractions, and design patterns (e.g. `.o-layout {}`).
* `/components`: Discrete, complete chunks of UI (e.g. `.c-carousel {}`). This is the one layer that neo doesn’t provide code for, as this is completely your terrain.
* `/utilities`: High-specificity, very explicit selectors. Overrides and helper
  classes (e.g. `.u-hidden {}`).

Following this structure allows you to intersperse neo’s code with your own, so that your `main.scss` file might look something like this:

```scss
// SETTINGS
// `@use` in partials as needed, no need to include here.

// TOOLS
// `@use` in partials as needed, no need to include here.

// GENERIC
@use 'node_modules/neo/generic/box-sizing';
@use 'node_modules/neo/generic/normalize';
@use 'node_modules/neo/generic/shared';

// ELEMENTS
@use 'node_modules/neo/elements/page';
@use 'node_modules/neo/elements/headings';
@use 'elements/links';
@use 'elements/quotes';

// OBJECTS
@use 'node_modules/neo/objects/layout';
@use 'node_modules/neo/objects/media';
@use 'node_modules/neo/objects/flag';
@use 'node_modules/neo/objects/list-bare';
@use 'node_modules/neo/objects/list-inline';
@use 'node_modules/neo/objects/box';
@use 'node_modules/neo/objects/block';
@use 'node_modules/neo/objects/table';

// COMPONENTS
@use 'components/buttons';
@use 'components/page-head';
@use 'components/page-foot';
@use 'components/site-nav';
@use 'components/ads';
@use 'components/promo';

// UTILITIES
@use 'node_modules/neo/utilities/widths';
@use 'node_modules/neo/utilities/headings';
@use 'node_modules/neo/utilities/spacings';
```

**NOTE:** Every `@use` above which begins with 'node_modules' is neo core.

Having your own and neo’s partials interlaced like this is one of the real strengths of neo.

## Core functionality

Before neo can do anything, it needs to know your base `font-size` and `line-height`. These settings are stored in `settings/_core` (as `$neo-global-font-size` and `$neo-global-line-height`), and can be overridden in the same way you’d [override any of neo’s config](#modifying-neo).

Probably the most opinionated thing neo will ever do is reassign your `$neo-global-line-height` variable to `$neo-global-spacing-unit`. This value then becomes the cornerstone of your UI, acting as the default margin and padding value for any components that require it.

While this might seem overly opinionated, it does mean that:

1. **You get a free vertical rhythm** because everything sits on a multiple of your baseline, and…
2. **We reduce the amount of [magic numbers](http://csswizardry.com/2012/11/code-smells-in-css/#magic-numbers) in our codebase** because we can rationalise where the majority of values in our CSS came from.

## Modifying neo

neo is highly configurable, but **should not be edited directly**. The correct way to make changes to neo is to pass in variables while `@use`ing the specific file. Let’s take [`settings/_core`](https://github.com/tstahn/neo/blob/develop/settings/_core.scss) as an example—in this file we can see the variables `$neo-global-font-size` and `$neo-global-line-height`. If we want to keep these as-is then we needn’t do anything other than `@use` the file. If we wanted to change these values to `12px` and `18px` respectively (don’t worry, neo will convert these pixel values to rems for you) then we just need to pass those values in `@use`ing, thus:

```scss
@use 'node_modules/neo/settings/core' with (
  $neo-global-font-size:   12px,
  $neo-global-line-height: 18px
);
```

The same goes for any neo module: you can configure it by overriding any
of its `!default` variables immediately in the `@use`:

```scss
@use 'node_modules/neo/objects/_wrapper' with (
  $neo-wrapper-width: 1480px
);

@use 'node_modules/neo/objects/_widths' with (
  $neo-fractions: 1 2 3 4 12
);
```

This method of modifying the framework means that you don’t need to edit any
files directly (thus making it easier to update the framework), but also means
that you’re not left with huge, bloated, monolithic variables files from which
you need to configure an entire library.

For further information on configuring modules, see the [Sass Documentation](https://sass-lang.com/documentation/at-rules/use/#configuration).

## Extending neo

To extend neo with your own code, simply create a partial in the `<section>/<file>` format, put it into the [appropriate directory](#css-directory-structure) and `@use` it in your `main.scss`.

But extending neo does not only mean adding your own partials to the project. Due to neo’s modular nature, you can also omit those partials of neo you don't need. But be aware that there are a few interdependencies between various neo partials. The only partial that is indispensable for the framework to work properly is `settings.core`, though. But we recommend using all partials from the `/settings`, `/tools` and `/generic` layer.

### Aliases

In order to avoid clashes with your own code, all of neo’s mixins and
variables are namespaced with `neo-`, for example: `$neo-global-spacing-unit`.
These variables and mixins can become very tedious and time-consuming to type
over and over, so it is recommended that you alias them to something a little
shorter. You can do this by creating a `aliases` file
(`tools/_aliases.scss`) which would be populated with code like this:

```scss
@use 'node_modules/neo/settings/core';

// Reassign `$neo-global-spacing-unit` to `$unit`.
$unit: core.$neo-global-spacing-unit;

// Reassign lengthy font-size mixin to `font-size()`.
@mixin font-size($args...) {
  @include neo-font-size($args...);
}
```

You can now use your own aliases onto neo’s defaults throughout your
project.

### Components

neo is a design-free, OOCSS framework—it does its best to provide zero
cosmetic styling. This means that neo can be used on any and all types of
project (and it has been) without dictating (or even suggesting) a
look-and-feel. If you do require a UI out of the box, then neo is probably
not the best tool for you. I’d recommend looking at a UI Toolkit instead.

Because neo does no cosmetic styling, it is up to you to author the
Components layer. Components are small partials that contain discrete chunks of
UI that utilise the layers that came before it, for example, a carousel, or a
dropdown nav, or an image gallery, and so on.

## Namespaces

You may have stumbled upon the “odd” way neo’s classes are prefixed. There are three different namespaces directly relevant to neo:

* `.o-`: Objects
* `.c-`: Components
* `.u-`: Utilities

In short: Every class in either of these three directories gets the appropriate prefix in its classname. All of neo’s classes in one of these three layers has this kind of prefix. Be sure to follow this convention in your own code as well to keep a consistent naming convention across your code base.

If you want to dive deeper into namespacing classes and want to know why this is a great idea, have a look at [this article](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/).

## Responsive

neo is built with as much extensibility as possible in mind. Adding full responsive functionality for every kind of module would pretty much kill the intended generic concept behind the framework.

The one opinionated decision we made was adding [Sass-MQ](https://github.com/sass-mq/sass-mq) as a dependency, to provide at least a full working responsive grid off-the-shelf. So if you need media-query support in your own Sass code, have a look at the [Sass-MQ documentation on how to use it](https://github.com/sass-mq/sass-mq#how-to-use-it) properly.

**NOTE: If you've installed neo neither with npm nor with yarn, make sure that Sass-MQ is properly imported in your `main.scss` in the tools layer.**

If you want to use another media-query library like [@include-media](http://include-media.com/) or [sass-mediaqueries](https://github.com/paranoida/sass-mediaqueries), feel free to do so. But in this case you have to manage your [responsive widths classes](https://github.com/tstahn/neo/blob/develop/utilities/_utilities.widths.scss) yourself.

## Providing plugins for neo

Since neo just provides very generic modules, there are probably modules you will write anew in every project. Although these modules might seem generic enough to you to be integrated into the core framework, we probably will consider it as not generic enough ([we'd appreciate every idea](https://github.com/tstahn/neo/blob/develop/CONTRIBUTING.md), though!).
But just because we are not willing to include a module you consider being useful, does not mean other neo users shall not benefit from such an useful module. Due to neo’s modular architecture, it's totally possible and we even welcome it, that these modules are published as kind of neo plugins by you in a separate repository.

We'd love to see that the framework gets extended through the contribution of you and your plugins!

## Prerequisites

Make sure you have at least Dart Sass 1.23.0 installed.

## Credits

* [Harry Roberts](https://github.com/csswizardry)
* [Anselm Hannemann](https://github.com/anselmh)
* [Dennis Heibült](https://github.com/csshugs)
* [Florian Bouvot](https://github.com/florianbouvot)
* [Nenad Jelovac](https://github.com/nenadjelovac)
