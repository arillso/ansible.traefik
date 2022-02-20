# Ansible Role: traefik

[![Build Status](https://img.shields.io/travis/arillso/ansible.traefik.svg?branch=master&style=popout-square)](https://travis-ci.org/arillso/ansible.traefik)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-traefik-blue.svg?style=popout-square)](https://galaxy.ansible.com/arillso/traefik)
[![Ansible Role](https://img.shields.io/ansible/role/d/48963.svg?style=popout-square)](https://galaxy.ansible.com/arillso/traefik)

<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Description](#description)
- [Installation](#installation)
- [Requirements](#requirements)
- [Role Variables](#role-variables)
	- [In-Depth Configuration](#in-depth-configuration)
- [Compatibility with  sbaerlocher/ansible.traefik](#compatibility-with-sbaerlocheransibletraefik)
	- [Variables which need manual action](#variables-which-need-manual-action)
		- [`traefik_configuration_file`](#traefikconfigurationfile)
		- [`traefik_api`](#traefikapi)
		- [`traefik_ping`](#traefikping)

<!-- /TOC -->

## Description

[Traefik](https://docs.traefik.io/v2.0) is a reverse proxy written in Go.
It can be used in multiple situations with many providers (Kubernetes, Swarm,
...). Version 2 is also capable of TCP routing.

This role sets up traefik on a host as reverse proxy and load balancer. This
allows you, to use one server as a host for multiple dockerized applications.

> **Note:** This role allows you to use one (1) server as a host for many
> applications. Depending on your usecase, this might not be what you are
> looking for. For services that need to be highly-available, consider using
> Kubernetes or other systems and setup traefik there.

## Installation

```bash
ansible-galaxy install arillso.traefik
```

## Requirements

- Docker

## Role Variables

Traefik v2.0 onwards supports yaml configuration. This role uses this to generate
the configuration directly from the given ansible variables.
There are certain quick-setup variables, which allow you to setup a simple
instance, but there is also the option to fully configure every key yourself.
The quick-setup allows you to:

- Setup a lets-encrypt based certificate resolver
- Setup standard entrypoints
- Setup standard Docker provider

The quick-setup variables are prefixed with `traefik_qs_`.

| Name                              | Default                      | Description                                                      |
| :-------------------------------- | :--------------------------- | :--------------------------------------------------------------- |
| `traefik_dir`                     | `/etc/traefik`               | where to store traefik data                                      |
| `traefik_hostname`                | `"{{ inventory_hostname }}"` | the hostname of this instance                                    |
| `traefik_network`                 | `traefik_proxy`              | the name of the generated network                                |
| `traefik_qs_send_anonymous_usage` | `false`                      | wether to send anonymous usage                                   |
| `traefik_qs_https`                | `false`                      | wether to setup a https endpoint                                 |
| `traefik_qs_https_redirect`       | `false`                      | wether to setup a redirection to https                           |
| `traefik_qs_https_le`             | `false`                      | wether to setup letsencrypt using tls (only if https is enabled) |
| `traefik_qs_https_le_mail`        | undefined                    | the email to use for letsencrypt (**Required**)                  |
| `traefik_qs_log_level`            | `ERROR`                      | the loglevel to apply                                            |
| `traefik_qs_exposedbydefault`       | `true`                       | If set to false, services that don't have a traefik.enable=true label will be ignored from the resulting routing configuration. |
| `traefik_use_dynamic_file_config` | `false` | Enable use of dynamic configurations with traefik. This is required for using `traefik_qs_tls_options` and `traefik_qs_middlewares`. It can also be used to load custom configurations. Just put the in the directory `traefik_dynamic_file_config_dir`. |
| `traefik_dynamic_file_config_dir` | `{{ traefik_dir }}/dynamic_conf` | Directory to store dynamic configurations to be used by traefik. |
| `traefik_qs_tls_options`            | `false`                      | Three different setups for tls options (modern, intermediate, old) are created  according to https://ssl-config.mozilla.org/#server=traefik. |
| `traefik_qs_middlewares`            | `false`                      | Setup default middleware config for hsts-header, xssfilter-header |
| `traefik_container_name`          | `'traefik'`                  | the container name                                               |
| `traefik_network_name`            | `'traefik_proxy'`            | the network name                                                 |
| `traefik_network_ipam_subnet`     | `'172.16.1.0/24'`            | subnet                                                           |
| `traefik_network_ipam_gateway`    | `'172.16.1.1'`               | gateway                                                          |
| `traefik_network_ipam_iprange`    | `'172.16.1.0/24'`            | iprange                                                          |
| `traefik_image`                   | `'traefik'`                  | the image used                                                   |
| `traefik_add_volumes`             | `[]`                         | additional volumes to mount                                      |
| `traefik_ports`                   | `['80:80', '443:443']`       | the ports shared                                                 |
| `traefik_labels`                  | `{}`                         | labels to set on the traefik container.                          |

The default names of the generated configs are:

- Entrypoints:
  - `http`
  - `https`
- Providers:
  - `docker`
  - `file` (only if `traefik_use_dynamic_file_config` is set to true)
- Certificate Resolvers:
  - `letsencrypt`

### In-Depth Configuration

As stated before, this role also allows you to configure traefik in-depth by
using the traefik yaml config. The following variables can be used:

| Name                                    | Default   | Description                                                                    |
| :-------------------------------------- | :-------- | ------------------------------------------------------------------------------ |
| `traefik_confkey_global`                | undefined | [see Docs 📑](https://docs.traefik.io/reference/static-configuration/file/)    |
| `traefik_confkey_serversTransport`      | undefined | [see Docs 📑](https://docs.traefik.io/reference/static-configuration/cli-ref/) |
| `traefik_confkey_entryPoints`           | undefined | [see Docs 📑](https://docs.traefik.io/routing/entrypoints/#entrypoints)        |
| `traefik_confkey_providers`             | undefined | [see Docs 📑](https://docs.traefik.io/routing/providers/docker/)               |
| `traefik_confkey_api`                   | undefined | [see Docs 📑](https://docs.traefik.io/operations/api/)                         |
| `traefik_confkey_metrics`               | undefined | [see Docs 📑](https://docs.traefik.io/observability/metrics/overview/)         |
| `traefik_confkey_ping`                  | undefined | [see Docs 📑](https://docs.traefik.io/operations/ping/)                        |
| `traefik_confkey_log`                   | undefined | [see Docs 📑](https://docs.traefik.io/observability/logs/)                     |
| `traefik_confkey_accessLog`             | undefined | [see Docs 📑](https://docs.traefik.io/observability/access-logs/)              |
| `traefik_confkey_tracing`               | undefined | [see Docs 📑](https://docs.traefik.io/observability/tracing/overview/)         |
| `traefik_confkey_hostResolver`          | undefined | [see Docs 📑](https://docs.traefik.io/reference/static-configuration/file/)    |
| `traefik_confkey_certificatesResolvers` | undefined | [see Docs 📑](https://docs.traefik.io/https/acme/#certificate-resolvers)       |

These keys are merged into the configuration **after** the quick-setup config using
the [`combine()`](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#combining-hashes-dictionaries)
filter in non recursive mode. This allows you to add configuration options as
you need them. If you want to overwrite the quick-setup items, use their key
(as specified above).

#### Dynamic file provider
A dynamic file provider is setup if `traefik_use_dynamic_file_config` is set tu true (disabled by default). It watches a directory which can be configured by `traefik_dynamic_file_config_dir`. This directory is used with the options `traefik_qs_tls_options` and/or `traefik_qs_middlewares`. By setting `traefik_qs_tls_options` and/or `traefik_qs_middlewares` to true specific config files are autogenerated `qs_traefik_tls_options.yml` and/or `qs_traefik_middlewares.yml`. The watched directory can also be used to add/load custom configurations.

##### qs_traefik_tls_options
To use the `qs_traefik_tls_options` option set it and `traefik_use_dynamic_file_config` to true. You can then use three predefined settings  (modern, intermediate, old) to secure tls sessions. To use them just set the according tls settings using labels to configure traefik. The example below show the used lables to use letsencrypt and intermediate tls configuration. https is uses a the entrypoint in traefik an everything is forwarded to port 80 of the container.

```yaml
# May be unnecessary depending on Traefik config, but can't hurt
traefik.enable: 'true'
# The  container will receive traffic from these subdomains
traefik.http.routers.uniqueconfigname.rule: 'Host(`www.example.com`)'
# address the entrypoint used in traefik config
traefik.http.routers.uniqueconfigname.entrypoints: 'https'
# (The 'default' certificate resolver must be defined in Traefik config)
traefik.http.routers.uniqueconfigname.tls.certResolver: 'letsencrypt'
traefik.http.routers.uniqueconfigname.tls.options: 'intermediate@file'
# address the internal destination
traefik.http.services.uniqueconfigname.loadbalancer.server.port: '80'
```

##### qs_traefik_middlewares
To use the `qs_traefik_middlewares` option set it and `traefik_use_dynamic_file_config` to true. You can then use two predefined settings (hsts-header, xssfilter-header) to secure the web sessions. To use them just set the according middleware settings using labels to configure traefik. The example below show the used lables to use letsencrypt, hsts-header and xssfilter-header configuration. https is uses a the entrypoint in traefik an everything is forwarded to port 80 of the container.

```yaml
# May be unnecessary depending on Traefik config, but can't hurt
traefik.enable: 'true'
# The  container will receive traffic from these subdomains
traefik.http.routers.uniqueconfigname.rule: 'Host(`www.example.com`)'
# address the entrypoint used in traefik config
traefik.http.routers.uniqueconfigname.entrypoints: 'https'
# (The 'default' certificate resolver must be defined in Traefik config)
traefik.http.routers.uniqueconfigname.tls.certResolver: 'letsencrypt'
traefik.http.routers.uniqueconfigname.middlewares: hsts-header@file,xssfilter-header@file
# address the internal destination
traefik.http.services.uniqueconfigname.loadbalancer.server.port: '80'
```

## Compatibility with sbaerlocher/ansible.traefik

This role is intended as a continuation of the
[sbaerlocher/ansible.traefik](https://github.com/sbaerlocher/ansible.traefik)
role for traefik v2. Most of the variables set for said role will continue
to work in this role, except for three special cases, where you **must**
recreate a custom configuration using the `_confkey_` variables.
These are explained in the following sections.

### Variables which need manual action

#### `traefik_configuration_file`

Using the `traefik_configuration_file` has no influence on your installation.
The configuration of Traefik has changed with the introduction of v2 and is not
backwards compatible. Use the [Traefik docs](https://docs.traefik.io/reference/static-configuration/file/)
to recreate your custom configuration using the `_confkey_` variables.

#### `traefik_api`

The way API is defined in Traefik v2 allows you, to use [several diffrent
configurations](https://docs.traefik.io/operations/api/). For the sake of
simplicity, we dropped the automatic generation of an api config, as it not
simply mergeable with a custom config and could lead to unforseen side effects.

To setup a simple, insecure api on container port `8080`, use the following
config (**Note**: this example is insecure, please consider securing your api
for use in production):

```yaml
traefik_confkey_api:
  insecure: true
  dashboard: true # use this for enabling the dashboard
traefik_ports:
  - '80:80'
  - '443:443'
  - '8080:8080'
```

This will automatically configure an [entrypoint on port `8080`](https://docs.traefik.io/operations/api/).

#### `traefik_ping`

Similar to the api definition, the ping definition allows custom configuration
over multiple diffrent configuration keys, making an automatically generated
config unfeasable.

Follow the [Traefik config docs about ping](https://docs.traefik.io/operations/ping/)
to find the configuration you want to apply. As an Example, take a look at this
config, which will expose the ping endpoint on port `8082`:

```yaml
traefik_confkey_entryPoints:
  ping:
    address: ':8082'
traefik_confkey_ping:
  entryPoint: 'ping'
traefik_ports:
  - '80:80'
  - '443:443'
  - '8082:8082'
```
