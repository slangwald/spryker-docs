---
title: Configuring access to private repositories
description: Configure your local environment to access private repositories.
last_updated: Jun 16, 2021
template: howto-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/configuring-access-to-private-repositories
originalArticleId: 6d136e03-869c-4adf-b8d3-0ea69c2589e0
redirect_from:
  - /2021080/docs/configuring-access-to-private-repositories
  - /2021080/docs/en/configuring-access-to-private-repositories
  - /docs/configuring-access-to-private-repositories
  - /docs/en/configuring-access-to-private-repositories
  - /docs/scos/dev/the-docker-sdk/202204.0/configuring-access-to-private-repositories.html
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
  - title: Configuring debugging in Docker
    link: docs/scos/dev/the-docker-sdk/page.version/configuring-debugging-in-docker.html
  - title: Running tests with the Docker SDK
    link: docs/scos/dev/the-docker-sdk/page.version/choosing-a-docker-sdk-version.html
---

This document describes how to configure an environment to allow the Docker SDK access private repositories.

## In what cases do I need to configure access to private repositories?

You need to configure access to private repositories in the following cases:

1. You have a private repository mentioned in `composer.json`:

```json
{
    "require": {
        "my-repo": "dev-master"
    },
    "repositories": [
        {
            "type": "git",
            "url": "git@github.com:my-org/my-repo.git"
        }
    ]
}
```

2. Running `docker/sdk up` returns an error similar to the following:

```
Cloning into '/data/vendor/my-org/my-repo'...
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

## Configuring an environment to access private repositories

To configure an environment to access private reporitories:

1. Add the `.known_hosts` file with the list of domains of VCS services into the project root. Example:
```
github.com
bitbucket.org
gitlab.my-org.com
```

2. Configure authentication of Composer to VCS services using one of the following options:

* [Configuring SSH agent authentication for Composer](#configuring-ssh-agent-authentication-for-composer). We recommend this option for development purposes.
* [Configuring the Composer authentication environment variable](#configuring-the-composer-authentication-environment-variable). We recommend this option for setting up CI/CD pipelines.


### Configuring SSH agent authentication for Composer

To configure SSH agent:

1. Ensure that `GITHUB_TOKEN` and `COMPOSER_AUTH` environment variables are not set:

```bash
unset GITHUB_TOKEN
unset COMPOSER_AUTH
```

2. Prepare SSH agent by adding your private keys:

```bash
eval $(ssh-agent)
ssh-add -K ~/.ssh/id_rsa
```

3. MacOS and Windows: For Docker Desktop to fetch the changes, restart the OS.

4. Re-build the application:

```bash
docker/sdk up --build
```

### Configuring the Composer authentication environment variable

To configure the Composer authentication environment variable:

1. Create access tokens in your VCS services.
2. Prepare a `COMPOSER_AUTH` environment variable with the VCS tokens you've created in JSON:

   * GitHub:

    ```json
    {
        "github-oauth": {
            "github.com": "{GITHUB_TOKEN}"
        }
    }
    ```

   * BitBucket:

    ```json
    {
        "bitbucket-oauth": {
            "bitbucket.org": {
                "consumer-key": "{BITBUCKET_KEY}",
                "consumer-secret": "{BITBUCKET_SECRET}"
            }
        }
    }
    ```

    * GitLab

    ```json
    {
        "gitlab-token": {
            "mysprykershop.com": "{GITLAB_TOKEN}"
        }
    }
    ```

To learn about Composer authentication variables, see [COMPOSER_AUTH](https://getcomposer.org/doc/03-cli.md#composer-auth) and [Custom token authentication](https://getcomposer.org/doc/articles/authentication-for-private-packages.md#custom-token-authentication)

3. Enable the environment variable using one of the following options:

* Export the environment variable taking Bash escaping rules into consideration:

```bash
export COMPOSER_AUTH="{\"github-oauth\":{\"github.com\":\"{GITHUB_TOKEN}\"},\"gitlab-oauth\":{\"gitlab.com\":\"{GITLAB_TOKEN}\"},\"bitbucket-oauth\":{\"bitbucket.org\": {\"consumer-key\": \"{BITBUCKET_KEY}\", \"consumer-secret\": \"{BITBUCKET_SECRET}\"{% raw %}}}{% endraw %}}"
```

* Add the environment variable to your development environment by editing `~/.bash_profile` or `~/.zshenv`:

```bash
export COMPOSER_AUTH="{\"github-oauth\":{\"github.com\":\"{GITHUB_TOKEN}\"},\"gitlab-oauth\":{\"gitlab.com\":\"{GITLAB_TOKEN}\"},\"bitbucket-oauth\":{\"bitbucket.org\": {\"consumer-key\": \"{BITBUCKET_KEY}\", \"consumer-secret\": \"{BITBUCKET_SECRET}\"{% raw %}}}{% endraw %}}"
```

4. Re-build the application:

```bash
docker/sdk up --build
```

You've configured authentication to your private repositories.
