## Features

### Language detection

Linguist defines a list of all languages known to GitHub in a [yaml file](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml).

Most languages are detected by their file extension. For disambiguating between files with common extensions, we first apply some common-sense heuristics to pick out obvious languages. After that, we use a
[statistical
classifier](https://github.com/github/linguist/blob/master/lib/linguist/classifier.rb).
This process can help us tell the difference between, for example, `.h` files which could be either C, C++, or Obj-C.

```ruby

Linguist::FileBlob.new("lib/linguist.rb").language.name #=> "Ruby"

Linguist::FileBlob.new("bin/linguist").language.name #=> "Ruby"
```

See [lib/linguist/language.rb](https://github.com/github/linguist/blob/master/lib/linguist/language.rb) and [lib/linguist/languages.yml](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml).

### Syntax Highlighting

Syntax highlighting in GitHub is performed using TextMate-compatible grammars. These are the same grammars that TextMate, Sublime Text and Atom use.

Every language in `languages.yml` is mapped to its corresponding TM `scope`. This scope will be used when picking up a grammar for highlighting. **When adding a new language to Linguist, please add its corresponding scope too (assuming there's an existing TextMate bundle, Sublime Text package, or Atom package) so syntax highlighting works for it**.

### Stats

#### Ignore vendored files

Checking other code into your git repo is a common practice. But this often inflates your project's language stats and may even cause your project to be labeled as another language. We are able to identify some of these files and directories and exclude them.

```ruby
Linguist::FileBlob.new("vendor/plugins/foo.rb").vendored? # => true
```

See [Linguist::BlobHelper#vendored?](https://github.com/github/linguist/blob/master/lib/linguist/blob_helper.rb) and [lib/linguist/vendor.yml](https://github.com/github/linguist/blob/master/lib/linguist/vendor.yml).

#### Generated file detection

Not all plain text files are true source files. Generated files like minified js and compiled CoffeeScript can be detected and excluded from language stats. As an extra bonus, these files are suppressed in diffs.

```ruby
Linguist::FileBlob.new("underscore.min.js").generated? # => true
```

See [Linguist::Generated#generated?](https://github.com/github/linguist/blob/master/lib/linguist/generated.rb).

## Overrides

Linguist supports custom overrides for language definitions and vendored paths. Add a `.gitattributes` file to your project using the keys `linguist-language` and `linguist-vendored` with the standard git-style path matchers for the files you want to override.

Please note that the overrides currently only affect the language statistics for a repository and not the syntax-highlighting of files.

```
$ cat .gitattributes
*.rb linguist-language=Java

$ linguist --breakdown
100.00% Java

Java:
ruby_file.rb
```

By default, Linguist treats all of the paths defined in [lib/linguist/vendor.yml](https://github.com/github/linguist/blob/master/lib/linguist/vendor.yml) as vendored and therefore doesn't include them in the language statistics for a repository. Use the `linguist-vendored` attribute to vendor or un-vendor paths.

```
$ cat .gitattributes
special-vendored-path/* linguist-vendored
jquery.js linguist-vendored=false
```

## Installation

Github.com is usually running the latest version of the `github-linguist` gem that is released on [RubyGems.org](http://rubygems.org/gems/github-linguist).

But for development you are going to want to checkout out the source. To get it, clone the repo and run [Bundler](http://gembundler.com/) to install its dependencies.

    git clone https://github.com/github/linguist.git
    cd linguist/
    script/bootstrap

## Usage

```ruby
require 'rugged'
require 'linguist'

repo = Rugged::Repository.new('.')
project = Linguist::Repository.new(repo, repo.head.target_id)
project.language       #=> "Ruby"
project.languages      #=> { "Ruby" => 119387 }
```

These stats are also printed out by the `linguist` executable. You can use the
`--breakdown` flag, and the binary will also output the breakdown of files by language.

You can try running `linguist` on the root directory in this repository itself:

    $ bundle exec linguist --breakdown

    100.00% Ruby

    Ruby:
    Gemfile
    Rakefile
    bin/linguist
    github-linguist.gemspec
    lib/linguist.rb
    …


### A note on language extensions

Linguist has a number of methods available to it for identifying the language of a particular file. The initial lookup is based upon the extension of the file, possible file extensions are defined in an array called `extensions`. Take a look at this example for example for `Perl`:

```
Perl:
  type: programming
  ace_mode: perl
  color: "#0298c3"
  extensions:
  - .pl
  - .PL
  - .perl
  - .ph
  - .plx
  - .pm
  - .pod
  - .psgi
  interpreters:
  - perl
```
Any of the extensions defined are valid but the first in this array should be the most popular.



## Contributing

### Syntax highlighting looks wrong

Assuming your code is being detected as the right language (see above), in most cases this is due to a bug in the language grammar rather than a bug in Linguist. [`grammars.yml`][grammars] lists all the grammars we use for syntax highlighting on github.com. Find the one corresponding to your code's programming language and submit a bug report upstream. If you can, try to reproduce the highlighting problem in the text editor that the grammar is designed for (TextMate, Sublime Text, or Atom) and include that information in your bug report.

You can also try to fix the bug yourself and submit a Pull Request. [This piece from TextMate's documentation](http://manual.macromates.com/en/language_grammars) offers a good introduction on how to work with TextMate-compatible grammars. You can test grammars using [Lightshow](https://lightshow.githubapp.com).

Once the bug has been fixed upstream, please let us know and we'll pick it up for GitHub.

### Testing

But for development you are going to want to checkout out the source. To get it, clone the repo and run [Bundler](http://gembundler.com/) to install its dependencies.

git clone https://github.com/github/linguist.git
cd linguist/
script/bootstrap

To run the tests:

    bundle exec rake test

Sometimes getting the tests running can be too much work, especially if you don't have much Ruby experience. It's okay: be lazy and let our build bot [Travis](http://travis-ci.org/#!/github/linguist) run the tests for you. Just open a pull request and the bot will start cranking away.

Here's our current build status: [![Build Status](https://secure.travis-ci.org/github/linguist.png?branch=master)](http://travis-ci.org/github/linguist)
