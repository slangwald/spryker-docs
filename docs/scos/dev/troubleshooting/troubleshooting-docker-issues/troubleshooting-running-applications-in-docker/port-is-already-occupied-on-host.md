---
title: Port is already occupied on host
description: Learn how to fix the issue when port is already occupied on host
last_updated: Jun 16, 2021
template: troubleshooting-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/port-is-already-occupied-on-host
originalArticleId: cf038694-8d04-43cf-adc7-005de29058e2
redirect_from:
  - /2021080/docs/port-is-already-occupied-on-host
  - /2021080/docs/en/port-is-already-occupied-on-host
  - /docs/port-is-already-occupied-on-host
  - /docs/en/port-is-already-occupied-on-host
  - /v6/docs/port-is-already-occupied-on-host
  - /v6/docs/en/port-is-already-occupied-on-host
related:
  - title: 413 Request Entity Too Large
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/413-request-entity-too-large.html
  - title: An application is not reachable via http
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/an-application-is-not-reachable-via-http.html
  - title: MacOS and Windows - file synchronization issues in Development mode
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/macos-and-windows-file-synchronization-issues-in-development-mode.html
  - title: Mutagen error
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/mutagen-error.html
  - title: Mutagen synchronization issue
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/mutagen-synchronization-issue.html
  - title: Nginx welcome page
    link: docs/scos/dev/troubleshooting/troubleshooting-docker-issues/troubleshooting-running-applications-in-docker/nginx-welcome-page.html
---

## Description

Running the `docker/sdk up` console command returns an error similar to the following:

```bash
ERROR: for nginx_frontend Cannot start service nginx_frontend: driver failed programming external connectivity on endpoint spryker_nginx_frontend_1 (e4fdb360f6c9a3243c0a88fa74f8d377325f65b8cd2340b2dacb51377519c1cf): Error starting userland proxy: Bind for 0.0.0.0:80: unexpected error (Failure EADDRINUSE)
```

## Solution

1. Check what process occupies the port:

```bash
sudo lsof -nPi:80 | grep LISTEN
```

2. Stop the process or make it use a different port.
3. Run `docker/sdk up` again.
