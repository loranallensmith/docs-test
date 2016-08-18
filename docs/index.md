---
layout: default
title: Butternut - Mouthwatering asset compression
---

# Getting Started

Butternut is a plugin for bundling assets.
It integrates with multiple frameworks for a seamless deployment experience.

#### Features
* Custom commands for bundle compression
* Compressed bundle caching
* Bundling and caching of remote assets
* Dev mode, for easy site development with all assets
* Dev-only asset inclusion
* Works with [Octopress](http://octopress.org/)

## Installation

Copy or link `asset_bundler.rb` into your `_plugins` folder
for your project.

If your project is in a git repository, you can easily
manage your plugins by utilizing git submodules.

To install this plugin as a git submodule:

```
    git submodule add git://github.com/ipsum/butternut.git _plugins/asset_bundler`
```

To update:

```
    cd _plugins/asset_bundler
    git pull origin master
```

## Status

Currently only supports absolute asset paths in relation to your
source directory.  For example: `/css/mystyle.css` looks for a file
in `my_source_dir/css/mystyle.css`.

#### TODO

* Relative paths support
* CoffeeScript and LessCSS compilation support

#### Notes

**v0.05** - Changed from using Liquid::Tags to Liquid::Blocks.
This will break on existing bundle markup if you upgrade.

Why change it?  Well, Liquid::Tags have to be on one line,
whereas Liquid::Blocks do not, also it opens up some more
flexibility, as additional options could be included in the
tag text.

**v0.08** - Changed the `cdn` config parameter to `server_url` in order to be
more generic.  For the time being, `cdn` still works (see below).

Why change it?  There seemed to be a little confusion about the parameter name
and what the parameter does.

**v0.11** - `jekyll --watch` now turns on dev mode.  Removed code for
compatibility with versions of Jekyll pre 1.0.

Why change it?  `jekyll --watch` isn't really supported by the plugin anyway.
Also, Jekyll is changing and I don't want this to be any more of an
unmaintainable mess.

#### Is it production ready?

Consider this beta software, though for small Jekyll sites you
should have no problem using it.

## Usage

Once installed in your `_plugins` folder Jekyll Asset Bundler provides
Liquid::Blocks to use which will generate the appropriate
markup for including JavaScript and CSS.
Each of the following blocks consumes a [YAML](http://yaml.org)
formatted array.

The `dev_assets` tag includes the normal markup for the referenced
assets only in 'dev mode'.  The array items can either be local files
or urls for external scripts and are included as-is.
At any other time, it does nothing.
In a future version (hopefully soon), this will play a role in
utilizing things like LessCSS and CoffeeScript.

## Configuration

Some behavior can be modified with settings in your `_config.yml`.  The
following represents the default configuration:

```
    asset_bundler:
      compress:
        js: false
        css: false
      base_path: /bundles/
      server_url:
      remove_bundled: false
      dev: false
      bundle_name: false
      markup_templates:
        js: "<script type='text/javascript' src='{{url}}'></script>\n"
        css: "<link rel='stylesheet' type='text/css' href='{{url}}' />\n"
```

Here is a breakdown of each configuration option under the top level
`asset_bundler` key.

### compress:

Compresses nothing by default. Change the `js` and `css` keys to
modify compression behavior.

#### js:

To compress with the yui-compressor gem, use 'yui' here,
to compress with the closure compiler gem, use 'closure' here.

    compress:
      js: yui

To compress with a system command, enter it for the
appropriate asset type:

    compress:
      js: uglifyjs :infile -o :outfile -c

This example will run a `uglifyjs` command from your PATH
while substituting :outfile and :infile for temporary files
stored in `_asset_bundler_cache`.

If either :outfile or :infile are omitted, stdout and
stdin will be used.  *WARNING*, stdin and stdout are done
with IO.popen , which doesn't work on Windows

**Note:** Some have reported other issues when using the yui-compressor or
closure compiler gems on Windows. If you having trouble on windows, try
specifying a command as outlined above.

#### css:

Takes the exact same arguments as `js:`, with the exception
of the Google Closure Compiler ( it's JavaScript only ).

### base_path:

Where the bundles will be copied within your destination
folder.

Default: `/vendor/bundles/`.

### server_url:

**NOTE:** In v0.07 and earlier this setting was `cdn`.  The `cdn` key still
works and will act as an alias.  However, if the `server_url` key is set, it
will override `cdn`.

The root path of your server\_url or CDN (if you use one).
For example: http://my-cdn.cloudfront.net/

Jekyll Asset Bundler checks to make sure that this setting ends in a slash.

Default: ` ` (blank).

### remove_bundled:

If set to true, will remove the files included in your
bundles from the destination folder.

Default: `false`.

### dev:

**NOTE:** In v0.10 and earlier, dev mode was not enabled automatically for
`--auto` or `--watch` mode.

If set to true, enables dev mode.  When dev mode is active,
no bundles are created and all the referenced files are
included individually without modification.

Dev mode is also automatically enabled when using
`jekyll server`, `jekyll --watch` or when a top level configuration key: `dev`
is set to true.

Default: `false`.

### bundle_name:

Overrides bundle name. When false, MD5 hash of the content is used instead.

Default: `false`.

### markup_templates:

Use the relevant markup\_template options to override the default templates
for inserting bundles.  Each option is parsed with `Liquid::Template.parse`
and passed a `url` (String) parameter.

**Note:** if you want newlines to be passed in properly, be sure to quote your
templates in `_config.yml`.

#### js:

The default JavaScript markup is fairly verbose.  If you would like a modern
replacement, try `"<script src='{{url}}'></script>\n"`.

Default: `"<script type='text/javascript' src='{{url}}'></script>\n"`

#### css:

The default CSS is also verbose.  If you would like a modern
replacement, try `"<link rel=stylesheet href='{{url}}'>\n"`.

Default: `"<link rel='stylesheet' type='text/css' href='{{url}}' />\n"`

## Dependencies

Jekyll Asset Bundler uses the
[yui-compressor](https://github.com/sstephenson/ruby-yui-compressor) or
[closure-compiler](https://github.com/documentcloud/closure-compiler) gems
(when configured) and (obviously)
[Jekyll](http://jekyllrb.com).

## Author

Colin Kennedy, moshen on GitHub

## License

[MIT](http://colken.mit-license.org),
see LICENSE file.
