---
title: Running tests with the Docker SDK
description: Learn how to run tests with the Docker SDK.
last_updated: Jun 16, 2021
template: howto-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/running-tests-with-the-docker-sdk
originalArticleId: c3d69fda-9546-4de8-80e4-cfea6b3be6d0
redirect_from:
  - /2021080/docs/running-tests-with-the-docker-sdk
  - /2021080/docs/en/running-tests-with-the-docker-sdk
  - /docs/running-tests-with-the-docker-sdk
  - /docs/en/running-tests-with-the-docker-sdk
  - /docs/scos/dev/the-docker-sdk/202204.0/running-tests-with-the-docker-sdk.html
related:
  - title: The Docker SDK
    link: docs/scos/dev/the-docker-sdk/page.version/the-docker-sdk.html
  - title: Docker SDK quick start guide
    link: docs/scos/dev/the-docker-sdk/page.version/docker-sdk-quick-start-guide.html
  - title: Docker environment infrastructure
    link: docs/scos/dev/the-docker-sdk/page.version/docker-environment-infrastructure.html
  - title: Configuring services
    link: docs/scos/dev/the-docker-sdk/page.version/configure-services.html
  - title: Docker SDK configuration reference
    link: docs/scos/dev/the-docker-sdk/page.version/docker-sdk-configuration-reference.html
  - title: Choosing a Docker SDK version
    link: docs/scos/dev/the-docker-sdk/page.version/choosing-a-docker-sdk-version.html
  - title: Choosing a mount mode
    link: docs/scos/dev/the-docker-sdk/page.version/choosing-a-mount-mode.html
  - title: Configuring a mount mode
    link: docs/scos/dev/the-docker-sdk/page.version/configuring-a-mount-mode.html
  - title: Configuring access to private repositories
    link: docs/scos/dev/the-docker-sdk/page.version/configuring-access-to-private-repositories.html
  - title: Configuring debugging in Docker
    link: docs/scos/dev/the-docker-sdk/page.version/configuring-debugging-in-docker.html
---

This document describes how to run tests in different ways.

## What is a testing mode?

The Docker SDK lets you run applications in an environment configured for running tests.

In the testing mode, you can run tests in isolation, with full control of the system tested and all needed tooling in place. Once you activate the testing mode, the following happens:
1. The scheduler is enabled. Background jobs are stopped for preserving data consistency and full isolation.
2. The webdriver is enabled.

## Activating the testing mode and running tests

You can activate the testing mode in one of the following ways:

* Switch a running environment into the testing mode without rebuilding containers.
* Rebuild containers and run or restart an environment with the testing mode activated.

### Activating the testing mode in a running environment

1. Activate the testing mode in a running environment and enter the CLI container:

```bash
docker/sdk testing
```

2. In the CLI container, run Codeception:

```bash
codecept run
```
{% info_block infoBox "" %}

Same as other CLI commands, you can run the preceding commands as a single command: `docker/sdk testing codecept run`.

{% endinfo_block %}


### Running or restarting an environment in the testing mode

1. Restart all containers in the testing mode:

```bash
docker/sdk up -t
```

2. Switch to the CLI container:

```bash
docker/sdk cli -t
```

3. Run Codeception:

```bash
codecept run
```


## Running a specific category of tests

There are three categories of Spryker tests:

* Acceptance
* Functional
* Api

To run a specific category, run `codecept` with the respective configuration file:

```bash
codecept run -c codeception.{acceptance|functional|api}.yml
```

### Running a group of tests

To run one or more groups of tests, run `codecept run -g {Tax} -g {Customer}`.

### Excluding a group of tests

To exclude one or more groups of tests, run `codecept run -x {Tax} -x {Customer}`.


## Configuring a webdriver

To choose a webdriver, update `deploy.*.yml`.

Chromedriver is the default webdriver shipped with Docker SDK.

The Chromedriver configuration looks as follows in the deploy file:

```yaml
services:
    webdriver:
        engine: chromedriver
```        

See [webdriver:](/docs/scos/dev/the-docker-sdk/{{page.version}}/deploy-file/deploy-file-reference-1.0.html#webdriver) to learn more about webdriver configuration in the deploy file.

## Configure Codeception

To configure Codeception:

1. Prepare required environment variables:

```yaml
SPRYKER_TESTING_ENABLED: false
SPRYKER_TEST_WEB_DRIVER_HOST: '0.0.0.0'
SPRYKER_TEST_WEB_DRIVER_PORT: '4444'
SPRYKER_TEST_IN_BROWSER: 'chrome'
SPRYKER_TEST_BROWSER_BIN: '/usr/local/bin/chrome'
SPRYKER_TEST_WEB_DRIVER_BIN: 'vendor/bin/chromedriver'
```

2. Configure `codeception.*.yml`:

```yaml
extensions:
    enabled:
        - \SprykerTest\Shared\Testify\Helper\WebDriverHelper
        - \SprykerTest\Shared\Testify\Helper\SuiteFilterHelper
    config:
        \SprykerTest\Shared\Testify\Helper\WebDriverHelper:
            suites: ['Presentation']
            path: "%SPRYKER_TEST_WEB_DRIVER_BIN%"
            whitelisted-ips: ''
            webdriver-port: "%SPRYKER_TEST_WEB_DRIVER_PORT%"
            url-base: "/wd/hub"
            remote-enable: "%SPRYKER_TESTING_ENABLED%"
            host: "%SPRYKER_TEST_WEB_DRIVER_HOST%"
            browser: "%SPRYKER_TEST_IN_BROWSER%"
            capabilities:
                "goog:chromeOptions":
                    args: ["--headless", "--no-sandbox", "--disable-dev-shm-usage"]
                    binary: "%SPRYKER_TEST_BROWSER_BIN%"
        \SprykerTest\Shared\Testify\Helper\SuiteFilterHelper:
            inclusive:
                - Presentation

params:
    - tests/default.yml
    - env
```

## Stopping the testing mode

Once you've finished running tests, you can switch back to the development mode:

```bash
docker/sdk start
```

This stops or removes the webdriver, runs the scheduler, and deactivates the testing mode.
