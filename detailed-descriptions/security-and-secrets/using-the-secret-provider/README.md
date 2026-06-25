---
description: This page describes how to use the secret provider.
---

# Using the secret provider

The secret provider was introduced in Duplicati version 2.0.9.109 and aims to reduce the possibility of leaking passwords from Duplicati by not storing the passwords inside Duplicati.

To start using a secret provider you need to set only a single option:

```sh
--secret-provider=<url>
```

This will make the secret provider available for the remainder of the application.

You can then insert placeholder values where you want secrets to appear but without storing the actual secret in Duplicati. For commandline users, the secrets can appear in both the backend destination or in the options.

As an example:

```
duplicati backup \
  s3://example-bucket?auth-username=$s3-user&password=$s3-pass \
  --passphrase=$passphrase 
```

The secret provider will find the three keys prefixed with `$` and look them up with the secret provider. The provider will then be invoked to obtain the real values and the values will be replaced before running the operation. If the secret provider has these values:

```
s3-user=user
s3-pass=pass
passphrase=my-password
```

The example from above will then be updated internally, but without having the keys written on disk:

```
duplicati backup \
  s3://example-bucket?auth-username=user&password=pass \
  --passphrase=my-password
```

To ensure you never run with an empty string or a placeholder instead of the real value, all values requested needs to be in the storage provider, or the operation will fail with a message indicating which key was not found.

## Using secret provider in the user interface

To set up a secret provider for the [TrayIcon](../../../duplicati-programs/trayicon.md), [Agent](../../../duplicati-programs/agent.md) or [Server](../../../duplicati-programs/server.md) setups, you can add the `--secret-provider=url` to their respective commandlines, or you can add it via the user interface. Set it up in "Settings" -> "Advanced options" to have it apply to all backups, or via "Advanced options" on the individual backups.

<figure><picture><source srcset="../../../.gitbook/assets/Screenshot 2026-06-25 at 10.03.57.png" media="(prefers-color-scheme: dark)"><img src="../../../.gitbook/assets/Screenshot 2026-06-25 at 10.03.37.png" alt=""></picture><figcaption></figcaption></figure>
