# Changelog

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
and [human-readable changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Changed

- Adaptation of the task names
- Syntax adapted to standard
- Conversion of the traefik ocnfig dir variable from `traefik_host_vol` to `traefik_dir`

### Added

- Added the possibility to define container environment variable

## [1.0.2] - 2020-05-31

### Fixed

- recursive variable definition
- traefik volumes

## [1.0.1] - 2020-05-30

### Added

- Compatibility for [sbaerlocher/ansible.traefik](https://github.com/sbaerlocher/ansible.traefik)

### Changed

- no `set_fact` step for service generation anymore
- using `networks_cli_compatible` for service generation

### Fixed

- Certresolver config key is left undefined if no resolver is defined

## 1.0.0

- initial release

[unreleased]: https://github.com/arillso/ansible.traefik/compare/1.0.2...HEAD
[1.0.2]: https://github.com/arillso/ansible.traefik/compare/1.0.1...1.0.2
[1.0.1]: https://github.com/arillso/ansible.traefik/compare/1.0.0...1.0.1
