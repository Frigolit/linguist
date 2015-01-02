# Contributing

[open an issue][new-issue]

### Code is detected as the wrong language

This can usually be solved either by adding a new filename or file name extension to the language's entry in [`languages.yml`][languages] or adding more [samples][samples] for your language to the repository to make Linguist's classifier smarter.

### I want to add support for the `X` programming language

Great! The majority of contributions won't need to touch any Ruby code at all. We try only to add languages once they have some usage on GitHub. In most cases we prefer that languages be in use in hundreds of repositories before supporting them in Linguist.

To add support for a new language:

0. Add an entry for your language to [`languages.yml`][languages].
0. Add a grammar for your language. Please only add grammars that have a license that permits redistribution.
  0. Add your grammar as a submodule: `git submodule add https://github.com/JaneSmith/MyGrammar vendor/grammars/MyGrammar`.
  0. Add your grammar to [`grammars.yml`][grammars] by running `script/download-grammars --add vendor/grammars/MyGrammar`.
0. Add samples for your language to the [samples directory][samples] in the correct subdirectory.
0. Open a pull request, linking to a [GitHub search result](https://github.com/search?utf8=%E2%9C%93&q=extension%3Aboot+NOT+nothack&type=Code&ref=searchresults) showing in-the-wild usage.

### Releasing

If you are the current maintainer of this gem:

0. Create a branch for the release: `git checkout -b cut-release-vxx.xx.xx`
0. Make sure your local dependencies are up to date: `script/bootstrap`
0. Ensure that samples are updated: `bundle exec rake samples`
0. Ensure that tests are green: `bundle exec rake test`
0. Bump gem version in `lib/linguist/version.rb`, [like this](https://github.com/github/linguist/commit/8d2ea90a5ba3b2fe6e1508b7155aa4632eea2985).
0. Make a PR to github/linguist, [like this](https://github.com/github/linguist/pull/1238).
0. Build a local gem: `bundle exec rake build_gem`
0. Test the gem:
  0. Bump the Gemfile and Gemfile.lock versions for an app which relies on this gem
  0. Install the new gem locally
  0. Test behavior locally, branch deploy, whatever needs to happen
0. Merge github/linguist PR
0. Tag and push: `git tag vx.xx.xx; git push --tags`
0. Push to rubygems.org -- `gem push github-linguist-3.0.0.gem`

[grammars]: /grammars.yml
[languages]: /lib/linguist/languages.yml
[samples]: /samples
[new-issue]: https://github.com/github/linguist/issues/new
