---
title: Configure settings for the Config Server for Spring component in Azure Container Apps
description: Learn how to configure a Config Server for Spring component for your container app.
services: container-apps
author: craigshoemaker
ms.service: azure-container-apps
ms.custom: devx-track-azurecli
ms.topic: how-to
ms.date: 05/23/2024
ms.author: cshoe
---

# Configure settings for the Config Server for Spring component in Azure Container Apps

Config Server for Spring provides a centralized location to make configuration data available to multiple applications. Use the following guidance to learn how to configure and manage your Config Server for Spring component.

## Show

You can view the details of an individual component by name using the `show` command.

Before you run the following command, replace placeholders surrounded by `<>` with your values.

```azurecli
az containerapp env java-component config-server-for-spring show \
  --environment <ENVIRONMENT_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --name <JAVA_COMPONENT_NAME>
```

## List

You can list all registered Java components using the `list` command.

Before you run the following command, replace placeholders surrounded by `<>` with your values.

```azurecli
az containerapp env java-component list \
  --environment <ENVIRONMENT_NAME> \
  --resource-group <RESOURCE_GROUP>
```

## Bind

Use the `--bind` parameter of the `update` command to create a connection between the Config Server for Spring component and your container app.

Before you run the following command, replace placeholders surrounded by `<>` with your values.

```azurecli
az containerapp update \
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --bind <JAVA_COMPONENT_NAME>
```

## Unbind

To break the connection between your container app and the Config Server for Spring component, use the `--unbind` parameter of the `update` command.

Before you run the following command, replace placeholders surrounded by `<>` with your values.

``` azurecli
az containerapp update \
  --name <CONTAINER_APP_NAME> \
  --unbind <JAVA_COMPONENT_NAME> \
  --resource-group <RESOURCE_GROUP>
```

## Configuration options

The `az containerapp update` command uses the `--configuration` parameter to control how the Config Server for Spring is configured. You can use multiple parameters at once as long as they're separated by a space. You can find more details in [Spring Cloud Config Server](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/#_spring_cloud_config_server) docs.

The following table lists the different configuration values available.

### Git backend configurations

| Name | Description |
|---|---|
| `spring.cloud.config.server.git.uri`  <br/> `spring.cloud.config.server.git.repos.{repoName}.uri` | URI of remote repository. |
| `spring.cloud.config.server.git.username` <br/> `spring.cloud.config.server.git.repos.{repoName}.username`| Username for authentication with remote repository. |
| `spring.cloud.config.server.git.password` <br/> `spring.cloud.config.server.git.repos.{repoName}.password` | Password for authentication with remote repository. |
| `spring.cloud.config.server.git.search-paths` <br/> `spring.cloud.config.server.git.repos.{repoName}.search-paths`| Search paths to use within local working copy. By default searches only the root. |
| `spring.cloud.config.server.git.force-pull` <br/> `spring.cloud.config.server.git.repos.{repoName}.force-pull`| Flag to indicate that the repository should force pull. If true discard any local changes and take from remote repository. |
| `spring.cloud.config.server.git.default-label`  <br/> `spring.cloud.config.server.git.repos.{repoName}.default-label` | The default label used for Git is main. If you do not set spring.cloud.config.server.git.default-label and a branch named main does not exist, the config server will by default also try to checkout a branch named master. If you would like to disable the fallback branch behavior you can set spring.cloud.config.server.git.tryMasterBranch to false. |
| `spring.cloud.config.server.git.try-master-branch`  <br/> `spring.cloud.config.server.git.repos.{repoName}.try-master-branch`| The config server will by default try to checkout a branch named master. |
| `spring.cloud.config.server.git.skip-ssl-validation` <br/> `spring.cloud.config.server.git.repos.{repoName}.skip-ssl-validation`| The configuration server’s validation of the Git server’s SSL certificate can be disabled by setting the git.skipSslValidation property to true. |
| `spring.cloud.config.server.git.clone-on-start` <br/> `spring.cloud.config.server.git.repos.{repoName}.clone-on-start`| Flag to indicate that the repository should be cloned on startup (not on demand). Generally leads to slower startup but faster first query. |
| `spring.cloud.config.server.git.timeout`  <br/> `spring.cloud.config.server.git.repos.{repoName}.timeout`  | Timeout (in seconds) for obtaining HTTP or SSH connection (if applicable). Default 5 seconds. |
| `spring.cloud.config.server.git.refresh-rate`  <br/> `spring.cloud.config.server.git.repos.{repoName}.refresh-rate`| How often the config server will fetch updated configuration data from your Git backend. |
| `spring.cloud.config.server.git.private-key` <br/> `spring.cloud.config.server.git.repos.{repoName}.private-key`| Valid SSH private key. Must be set if ignore-local-ssh-settings is true and Git URI is SSH format. |
| `spring.cloud.config.server.git.host-key` <br/> `spring.cloud.config.server.git.repos.{repoName}.host-key`| Valid SSH host key. Must be set if host-key-algorithm is also set.  |
| `spring.cloud.config.server.git.host-key-algorithm`  <br/> `spring.cloud.config.server.git.repos.{repoName}.host-key-algorithm`  | One of ssh-dss, ssh-rsa, ssh-ed25519, ecdsa-sha2-nistp256, ecdsa-sha2-nistp384, or ecdsa-sha2-nistp521. Must be set if host-key is also set. |
| `spring.cloud.config.server.git.strict-host-key-checking`  <br/> `spring.cloud.config.server.git.repos.{repoName}.strict-host-key-checking`| true or false. If false, ignore errors with host key. |
| `spring.cloud.config.server.git.repos.{repoName}` | URI of remote repository. |
| `spring.cloud.config.server.git.repos.{repoName}.pattern` | The pattern format is a comma-separated list of {application}/{profile} names with wildcards. If {application}/{profile} does not match any of the patterns, it uses the default URI defined under. |

### Common configurations

- logging related configurations
  - [**logging.level.***](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-levels)
  - [**logging.group.***](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-groups)
  - Any other configurations under logging.* namespace should be forbidden, for example, writing log files by using `logging.file` should be forbidden.

- **spring.cloud.config.server.overrides**
  - Extra map for a property source to be sent to all clients unconditionally.

- **spring.cloud.config.override-none**
  - You can change the priority of all overrides in the client to be more like default values, letting applications supply their own values in environment variables or System properties, by setting the spring.cloud.config.override-none=true flag (the default is false) in the remote repository.

- **spring.cloud.config.allow-override**
  - If you enable config first bootstrap, you can allow client applications to override configuration from the config server by placing two properties within the applications configuration coming from the config server.

- **spring.cloud.config.server.health.**
  - You can configure the Health Indicator to check more applications along with custom profiles and custom labels

- **spring.cloud.config.server.accept-empty**
  - You can set `spring.cloud.config.server.accept-empty` to `false` so that the server returns an HTTP `404` status, if the application is not found. By default, this flag is set to `true`.

- **Encryption and decryption (symmetric)**
  - **encrypt.key**
    - It is convenient to use a symmetric key since it is a single property value to configure.
  - **spring.cloud.config.server.encrypt.enabled**
    - You can set this to `false`, to disable server-side decryption.

## Refresh

Services that consume properties need to know about the change before it happens. The default notification method for Config Server for Spring involves manually triggering the refresh event, such as refresh by call `https://<YOUR_CONFIG_CLIENT_HOST_NAME>/actuator/refresh`, which may not be feasible if there are many app instances.

Instead, you can automatically refresh values from Config Server by letting the config client poll for changes based on a refresh internal. Use the following steps to automatically refresh values from Config Server.

1. Register a scheduled task to refresh the context in a given interval, as shown in the following example.

    ``` Java
    @Configuration
    @AutoConfigureAfter({RefreshAutoConfiguration.class, RefreshEndpointAutoConfiguration.class})
    @EnableScheduling
    public class ConfigClientAutoRefreshConfiguration implements SchedulingConfigurer {
        @Value("${spring.cloud.config.refresh-interval:60}")
        private long refreshInterval;
        @Value("${spring.cloud.config.auto-refresh:false}")
        private boolean autoRefresh;
        private final RefreshEndpoint refreshEndpoint;
        public ConfigClientAutoRefreshConfiguration(RefreshEndpoint refreshEndpoint) {
            this.refreshEndpoint = refreshEndpoint;
        }
        @Override
        public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
            if (autoRefresh) {
                // set minimal refresh interval to 5 seconds
                refreshInterval = Math.max(refreshInterval, 5);
                scheduledTaskRegistrar.addFixedRateTask(refreshEndpoint::refresh,  Duration.ofSeconds(refreshInterval));
            }
        }
    }
    ```

1. Enable `autorefresh` and set the appropriate refresh interval in the *application.yml* file. In the following example, the client polls for a configuration change every 60 seconds, which is the minimum value you can set for a refresh interval.

    By default, `autorefresh` is set to `false`, and `refresh-interval` is set to 60 seconds.

    ``` yaml
    spring:
        cloud:
            config:
            auto-refresh: true
            refresh-interval: 60
    management:
        endpoints:
            web:
            exposure:
                include:
                - refresh
    ```

1. Add `@RefreshScope` in your code. In the following example, the variable `connectTimeout` is automatically refreshed every 60 seconds.

    ``` Java
    @RestController
    @RefreshScope
    public class HelloController {
        @Value("${timeout:4000}")
        private String connectTimeout;
    }
    ```

## Encryption and decryption with a symmetric key

### Server-side decryption

By default, server-side encryption is enabled. Use the following steps to enable decryption in your application.

1. Add the encrypted property in your *.properties* file in your git repository.

    For example, your file should resemble the following example:

    ```
    message={cipher}f43e3df3862ab196a4b367624a7d9b581e1c543610da353fbdd2477d60fb282f
    ```

1. Update the Config Server for Spring Java component to use the git repository that has the encrypted property and set the encryption key.

    Before you run the following command, replace placeholders surrounded by `<>` with your values.

    ```azurecli
    az containerapp env java-component config-server-for-spring update \
      --environment <ENVIRONMENT_NAME> \
      --resource-group <RESOURCE_GROUP> \
      --name <JAVA_COMPONENT_NAME> \
      --configuration spring.cloud.config.server.git.uri=<URI> encrypt.key=randomKey
    ```

### Client-side decryption

You can use client side decryption of properties by following the steps:

1. Add the encrypted property in your `*.properties*` file in your git repository.

1. Update the Config Server for Spring Java component to use the git repository that has the encrypted property and disable server-side decryption.

    Before you run the following command, replace placeholders surrounded by `<>` with your values.

    ```azurecli
    az containerapp env java-component config-server-for-spring update \
      --environment <ENVIRONMENT_NAME> \
      --resource-group <RESOURCE_GROUP> \
      --name <JAVA_COMPONENT_NAME> \
      --configuration spring.cloud.config.server.git.uri=<URI> spring.cloud.config.server.encrypt.enabled=false
    ```

1. In your client app, add the decryption key `ENCRYPT_KEY=randomKey` as an environment variable.

    Alternatively, if you include *spring-cloud-starter-bootstrap* on the `classpath`, or set `spring.cloud.bootstrap.enabled=true` as a system property, set `encrypt.key` in `bootstrap.properties`.

    Before you run the following command, replace placeholders surrounded by `<>` with your values.

    ```azurecli
    az containerapp update \
      --name <APP_NAME> \
      --resource-group <RESOURCE_GROUP> \
	    --set-env-vars "ENCRYPT_KEY=randomKey"
    ```

    ```
    encrypt:
      key: somerandomkey
    ```

## Next steps

> [!div class="nextstepaction"]
> [Tutorial: Connect to a managed Config Server for Spring](java-config-server.md)
