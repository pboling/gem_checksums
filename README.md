# GemChecksums

A ruby script, and rake task, to generate SHA-256 and SHA-512 checksums of RubyGem libraries, shipped as a RubyGem.

You may be familiar with the standard rake task `build:checksum` from RubyGems.
This gem ships an improved version as `build:checksums`, based on the
[RubyGems pull request and discussion here](https://github.com/rubygems/rubygems/pull/6022).

```shell
rake build:checksums
```

It is different from, and improves on, the standard rake task in that it:
- does various checks to ensure the generated checksums will be valid
- does `git commit` the generated checksums

## Installation

Install the gem and add to the gem's Gemfile by executing:

```bash
bundle add gem_checksums
```

Or add it as a development dependency to the gemspec:

```ruby
Gem::Specification.new do |spec|
  # ...
  spec.add_development_dependency("gem_checksums", "~> 1.0")
end
```

If bundler is not being used to manage dependencies, install the gem by executing:

```bash
gem install gem_checksums
```

## Usage

You may be familiar with the standard rake task `build:checksum` from RubyGems.
This gem ships an improved version as `build:checksums`, based on the
[RubyGems pull request and discussion here](https://github.com/rubygems/rubygems/pull/6022).

```shell
rake build:checksums
```

Generating checksums makes sense when you are building and releasing a gem, so how does it fit into that process?

### How To: Release gem with checksums generated by `gem_checksums`

NOTE: This is an example process which assumes your project has bundler binstubs, and a version.rb file,
with notes for `zsh` and `bash` shells.

1. Run `bin/setup && bin/rake` as a tests, coverage, & linting sanity check
2. Update the version number in `version.rb`
3. Run `bin/setup && bin/rake` again as a secondary check, and to update `Gemfile.lock`
4. Run `git commit -am "🔖 Prepare release v<VERSION>"` to commit the changes
5. Run `git push` to trigger the final CI pipeline before release, & merge PRs
    - NOTE: Remember to [check the build][🧪build]!
6. Run `export GIT_TRUNK_BRANCH_NAME="$(git remote show origin | grep 'HEAD branch' | cut -d ' ' -f5)" && echo $GIT_TRUNK_BRANCH_NAME`
7. Run `git checkout $GIT_TRUNK_BRANCH_NAME`
8. Run `git pull origin $GIT_TRUNK_BRANCH_NAME` to ensure you will release the latest trunk code
9. Set `SOURCE_DATE_EPOCH` so `rake build` and `rake release` use same timestamp, and generate same checksums
    - Run `export SOURCE_DATE_EPOCH=$EPOCHSECONDS && echo $SOURCE_DATE_EPOCH`
    - If the echo above has no output, then it didn't work.
    - Note that you'll need the `zsh/datetime` module, if running `zsh`.
    - In `bash` you can use `date +%s` instead, i.e. `export SOURCE_DATE_EPOCH=$(date +%s) && echo $SOURCE_DATE_EPOCH`
10. Run `bundle exec rake build`
11. Run `gem_checksums` (from this gem, and added to path in .envrc,
    more context [1][🔒️rubygems-checksums-pr] and [2][🔒️rubygems-guides-pr]) to create SHA-256 and SHA-512 checksums
    - Checksums will be committed automatically by the script, but not pushed
12. Run `bundle exec rake release` which will create a git tag for the version,
    push git commits and tags, and push the `.gem` file to [rubygems.org][💎rubygems]

[🧪build]: https://github.com/pboling/gem_checksums/actions
[🤝conduct]: https://github.com/pboling/gem_checksums/blob/main/CODE_OF_CONDUCT.md
[🖐contrib-rocks]: https://contrib.rocks
[🚎contributors]: https://gitlab.com/pboling/gem_checksums/-/graphs/main
[🖐contributors]: https://github.com/pboling/gem_checksums/graphs/contributors
[🖐contributors-img]: https://contrib.rocks/image?repo=pboling/gem_checksums
[📗keep-changelog]: https://keepachangelog.com/en/1.0.0/
[📗keep-changelog-img]: https://img.shields.io/badge/keep--a--changelog-1.0.0-FFDD67.svg?style=flat
[💎rubygems]: https://rubygems.org
[🔒️rubygems-security-guide]: https://guides.rubygems.org/security/#building-gems
[🔒️rubygems-checksums-pr]: https://github.com/rubygems/rubygems/pull/6022
[🔒️rubygems-guides-pr]: https://github.com/rubygems/guides/pull/325
[🚎src-main]: https://github.com/pboling/gem_checksums

It is different from, and improves on, the standard rake task in that it:
- does various checks to ensure the generated checksums will be valid
- does `git commit` the generated checksums

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and the created tag, and push the `.gem` file to [rubygems.org](https://rubygems.org).

### TODOs

- [] Prepend `rake build` task with check for `SOURCE_DATE_EPOCH` environment variable, and raise error if not set.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/gem_checksums. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/[USERNAME]/gem_checksums/blob/main/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the GemChecksums project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/[USERNAME]/gem_checksums/blob/main/CODE_OF_CONDUCT.md).
