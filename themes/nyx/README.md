# Nyx

Sample Image Placeholder

## General information

## Features

- User configureable **dark/light modes**
- Code highlighting thanks to [**PrismJS**](https://prismjs.com)

## Getting Started

You can download the theme manually by going to [https://github.com/grahamplata/nyx.git](https://github.com/grahamplata/nyx.git) and pasting it to `themes/nyx` in your root directory.

As a clone:

``` bash
$ git clone https://github.com/grahamplata/nyx.git themes/nyx
```

As a git submodule:

``` bash
$ git submodule add https://github.com/grahamplata/nyx.git themes/nyx
```

## Configuration

The theme doesn't require any advanced configuration. Just copy:

``` toml
baseurl = "/"
languageCode = "en-us"
theme = "nyx"

[params]
  dateform        = "Jan 2, 2006"
  dateformShort   = "Jan 2"
  dateformNum     = "2006-01-02"
  dateformNumTime = "2006-01-02 15:04 -0700"

  # Set disableReadOtherPosts to true in order to hide the links to other posts.
  disableReadOtherPosts = false

  # Metadata mostly used in document's head
  description = "Homepage and blog by Djordje Atlialp"
  keywords = "homepage, blog, science, informatics, development, programming"
  images = [""]

  # Directory name of your blog content (default is `content/posts`)
  contentTypeName = "posts"

  # Default theme "light" or "dark"
  defaultTheme = "dark"

[languages]
  [languages.en]
    title = "Nyx"
    subtitle = "A simple theme for Hugo"
    keywords = ""
    copyright = ""
    readOtherPosts = "Read other posts"

    [languages.en.params.logo]
      logoText = "nyx"
      logoHomeLink = "/"
    # or
    #
    # path = "/img/your-example-logo.svg"
    # alt = "Your example logo alt text"

  # And you can even create generic menu
  [[menu.main]]
    identifier = "blog"
    name       = "Blog"
    url        = "/posts"
```

## How to run your site

From your Hugo project root directory run:

```shell
$ hugo server -t nyx #Navigate to localhost:1313
```

## Contributing

- For problems please use the [Issue Tracker](https://github.com/grahampalta/nyx/issues)
- For new features please open a [Pull Request](https://github.com/grahamplata/nyx/pulls).

## Licence

Copyright © 2020 Graham Plata

The theme is released under the MIT License. Check the [license](https://github.com/grahamplata/nyx/blob/master/LICENSE.md) for additional licensing information.
