# Agent Check: VoltDB

## Overview

This check monitors [VoltDB][1] through the Datadog Agent.

## Setup

Follow the instructions below to install and configure this check for an Agent running on a host. For containerized environments, see the [Autodiscovery Integration Templates][2] for guidance on applying these instructions.

**Note**: this check should be installed on one Agent per cluster only. If monitoring a cluster spread across several hosts, feel free to install an Agent on each host, but make sure that the integration is only configured on a single Agent.

### Installation

The VoltDB check is included in the [Datadog Agent][2] package.
No additional installation is needed on your server.

### Configuration

1. Add a `datadog-agent` user. You can do so by editing your VoltDB `deployment.xml` file. Note that no specific roles are required, so assign the built-in `user` role.

    ```xml
    <users>
        <!-- ... -->
        <user name="datadog-agent" password="<PASSWORD>" roles="user" />
    </users>
    ```

2. Edit the `voltdb.d/conf.yaml` file, in the `conf.d/` folder at the root of your Agent's configuration directory to start collecting your voltdb performance data. See the [sample voltdb.d/conf.yaml][3] for all available configuration options.

    ```yaml
    init_config:

    instances:
      - url: http://localhost:8080
        username: datadog-agent
        password: "<PASSWORD>"
    ```

3. [Restart the Agent][4].

#### TLS support

If [TLS/SSL][5] is enabled on the client HTTP port:

1. Prepare your certificate so that it is in PEM format.

    If using a locally created PKCS12 keystore created via `keytool`, use following command to export the certificate to PEM:

    ```bash
    openssl pkcs12 -in mydb.keystore -out /path/to/voltdb.cert
    ```

    **Note**: passphrase-protected certificates are not supported. If your keystore is protected behind a passphrase, consider exporting a password-protected certificate, then removing its password:

    ```bash
    openssl pkcs12 -in mydb.keystore -password "pass:<PASSWORD>" -out /path/to/voltdb.cert -passout pass:temp
    openssl rsa -in /path/to/voltdb.cert -passin pass:temp -out /path/to/voltdb.cert
    ```

2. In your instance configuration, point `url` to the TLS-enabled client endpoint, and set the `tls_cert` option. For example:

    ```yaml
    instances:
    - # ...
      url: https://localhost:8443
      tls_cert: /path/to/voltdb.cert
    ```

3. [Restart the Agent][4].

### Validation

[Run the Agent's status subcommand][6] and look for `voltdb` under the Checks section.

## Data Collected

### Metrics

See [metadata.csv][7] for a list of metrics provided by this check.

### Service Checks

**voltdb.can_connect**:<br>
Returns `CRITICAL` if the Agent cannot reach the configured VoltDB URL, `OK` otherwise.

### Events

This check does not include any events.

## Troubleshooting

Need help? Contact [Datadog support][8].

[1]: https://voltdb.com
[2]: https://docs.datadoghq.com/agent/kubernetes/integrations/
[3]: https://github.com/DataDog/integrations-core/blob/master/voltdb/datadog_checks/voltdb/data/conf.yaml.example
[4]: https://docs.datadoghq.com/agent/guide/agent-commands/#start-stop-and-restart-the-agent
[5]: https://docs.voltdb.com/UsingVoltDB/SecuritySSL.php
[6]: https://docs.datadoghq.com/agent/guide/agent-commands/#agent-status-and-information
[7]: https://github.com/DataDog/integrations-core/blob/master/voltdb/metadata.csv
[8]: https://docs.datadoghq.com/help/