### Porter's HashiCorp Plugins

This is a [Porter][porter] plugin to inject credentials to Porter bundle from hashicorp's [vault][vault].

#### Install

```
porter plugin install hashicorp --version v1.0.0 --url https://github.com/getporter/hashicorp-plugins/releases/download
```

Until the [upstream plugin] is updated to work with Porter v1, this is a fork with compatibility fixes.
For older versions of Porter, use version v0.1.0 of the original hashicorp plugin.
We only support the [KV Version 2][kv-v2] secret engine. Please raise an issue if you're looking for support for other secret engines.

[porter]: https://getporter.org/
[vault]: https://www.vaultproject.io/
[kv-v2]: https://www.vaultproject.io/api-docs/secret/kv/kv-v2
[upstream plugin]: https://github.com/dev-drprasad/porter-hashicorp-plugins

#### Configuration
To use vault plugin, add the following config to porter's config file (default location: `~/.porter/config.yaml`). Replace `vault_addr`, `vault_token` and `path_prefix` with proper values.

The example below retrieves the vault_token from the VAULT_TOKEN environment variable.
Do not store sensitive data in the Porter configuration file.

```yaml
default-secrets: "porter-secrets"

secrets:
  name: "porter-secrets"
  plugin: "hashicorp.vault"
  config:
    vault_addr: "http://vault.example.com:7500"
    path_prefix: "organization/team/project"
    vault_token: "${env.VAULT_TOKEN}"
```

## Config Parameters
#### path_prefix

`path_prefix` allows you to specify a prefix for your secret path.
Let's say you have a secret (`myawesomeproject`) with path `organization/team/project/myawesomeproject`, then you can configure `path_prefix` as `organization/team/project`.

#### porter_secret

You can optionally change where Porter saves secrets by setting `porter_secret`.
By default, Porter generated secrets are saved to PATH_PREFIX/SECRET_KEY/porter.

#### Secret Organization

The plugin resolves the secret using the secret value set in the Parameter or Credential Set, using the path prefix defined in the Porter configuration file.

```yaml
name: myparameterset
parameters:
  - name: mysql-connection-string
    source:
      secret: myapp/v1/connstr
```

The secret value can use sub-paths to further select the correct secret.
In the example above, the mysql-connection-string parameter resolves to the secret at PATH_PREFIX/myapp/v1/connstr.
