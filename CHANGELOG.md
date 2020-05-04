# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog][] and this project adheres to
[Semantic Versioning][]

[Keep a Changelog]: https://keepachangelog.com/en/1.0.0/
[Semantic Versioning]: https://semver.org/spec/v2.0.0.html

## [Unreleased][]

### Added

- Add check for missing notes file in copy command

### Changed

- Improve README

## [0.7.0][] - 2020-05-04

### Added

- Add `update` command for updating to the latest version
- Add this change log
- Add version badge to README

### Removed

- Remove `next` date alias for `nwd`

### Changed

- Change `prev` from alias for `pwd` to stand for previous day with notes
- Remove last trailing newline character when copying
- Improve README

## [0.6.0][] - 2020-05-03

### Changed

- Autodetect copy program and set `$JOT_COPY` automatically if missing
- Improve help command examples
- Improve README

## [0.5.0][] - 2020-05-01

### Added

- Add support for command-agnostic hooks that run before and after any other command-specific hooks

### Changed

- Improve README

## [0.4.0][] - 2020-05-01

### Added

- Add support for `-- dir` alias to target the `$JOT_HOME` directory when issuing custom commands

### Changed

- Set current working directory for commands and hooks to `$JOT_HOME`
- Improve README

## [0.3.0][] - 2020-05-01

### Removed

- Remove trailing / from `$JOT_HOME` and `$JOT_HOOKS`

### Changed

- Export `$JOT_HOME` so that it is available in hooks
- Improve README

## [0.2.0][] - 2020-05-01

### Added

- Include version command in help text

### Removed

- Remove redundant `-d today` option for `date`
- Remove redundant date case terms from `parse_date`

### Changed

- Embed source URL and license in script
- Improve README

## [0.1.0][] - 2020-05-01

- Initial release

[Unreleased]: https://github.com/agorf/jot/compare/0.7.0...HEAD
[0.7.0]: https://github.com/agorf/jot/compare/0.6.0...0.7.0
[0.6.0]: https://github.com/agorf/jot/compare/0.5.0...0.6.0
[0.5.0]: https://github.com/agorf/jot/compare/0.4.0...0.5.0
[0.4.0]: https://github.com/agorf/jot/compare/0.3.0...0.4.0
[0.3.0]: https://github.com/agorf/jot/compare/0.2.0...0.3.0
[0.2.0]: https://github.com/agorf/jot/compare/0.1.0...0.2.0
[0.1.0]: https://github.com/agorf/jot/releases/tag/0.1.0
