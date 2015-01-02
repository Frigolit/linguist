# Linguist

This library is used on GitHub.com to detect blob languages, ignore binary or vendored files, suppress generated files in diffs, and generate language breakdown graphs.

See [Troubleshooting](#troubleshooting) and [`CONTRIBUTING.md`](/CONTRIBUTING.md) before filing an issue or creating a pull request.

## Troubleshooting

### My repository is detected as the wrong language

![language stats bar](https://cloud.githubusercontent.com/assets/173/5562290/48e24654-8ddf-11e4-8fe7-735b0ce3a0d3.png)

The Language stats bar is built by aggregating the languages of each file in that repository. If it is reporting a language that you don't expect, click on the name of the language and to see a list of the files that are identified as that language. If there are files in your repository

consider moving the files into one of the

[overrides][]
