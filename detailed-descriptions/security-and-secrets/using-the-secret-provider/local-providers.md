---
description: >-
  This page describes the providers that operate locally on the machine they are
  running
---

# Local providers

## The Environment Variable provider

The simplest provider is the `env://` provider, which simply extracts environment variables and replaces those. There is no configuration needed for this provider, and the syntax for adding it is simply:

```
--secret-provider=env://
```

## The File Secret provider

The `file-secret://` provider supports reading secrets from a file containing a JSON encoded dictionary of key/value pairs. As an example, a file could look like:

```json
{
  "key1": "value1",
  "passphrase": "my password"
}
```

The file provider also supports files encrypted with [AESCrypt](../../../using-tools/encrypting-and-decrypting-files.md) and you supply the decryption key with the option `passphrase`. Suppose the file is encrypted with the key `mypassword` you can then configure the provider:

```
--secret-provider=file-secret:///home/user/secrets.json.aes?passphrase=my-password
```

To avoid passing the encryption key via a commandline, see [the section on how to inject the secret provider configuration via an environment variable](advanced-configurations.md#how-to-avoid-passing-credentials-on-the-commandline).

To encrypt the file, you can use the [AESCrypt tool](../../../duplicati-programs/command-line-interface-cli-1/sharpaescrypt.md) provided with Duplicati:

```
Duplicati.CommandLine.SharpAESCrypt.exe e my-password source.json destination.json.aes
```

## Credential Manager (Windows)

On Windows XP and later, the [Credential Manager](https://support.microsoft.com/en-us/windows/accessing-credential-manager-1b5c916a-6a16-889f-8581-fc16e8165ac0) can be used to securely store secrets. As the credentials are protected by the account login, there is no configuration needed, so the setup is simply:

```
--secret-provider=wincred://
```

The Windows Credential Manager can be accessed from the Control Panel and looks like this:

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 11.22.24.png" alt=""><figcaption></figcaption></figure>

The secrets created from Duplicati will show up as "Generic Credentials" but Duplicati can also read the Windows Credentials. You can click "Add a generic credential" to create a new secret that can be used by Duplicati.

## Using `libsecret` (Linux)

The[ `libsecret` implementation](https://gnome.pages.gitlab.gnome.org/libsecret/) stores various credentials on Linux and integrates with various UI applications to let the user approve or reject attempts to read secrets. The `libsecret` provider supports a single optional setting, `collection`, which indicates what collection to read from. If not supplied the default collection is used. The default collection is usually `login` on Gnome-based systems and `kdewallet` on KDE-based systems, and will typically be unlocked when the user logs in.

To use the `libsecret` provider, use this argument:

```
--secret-provider=libsecret://
```

If you are using a system with a Gnome-based desktop, such as Ubuntu, you can use the [Seahorse](https://wiki.gnome.org/Apps/Seahorse) application to manage your passwords. On KDE-based desktops you can use [KeepSecret](https://apps.kde.org/keepsecret/) or [KWalletManager](https://apps.kde.org/kwalletmanager5/).

On KDE, make sure you have enabled the "KDE Wallet Subsystem" and also checked the option to "Use KWallet for the Secret Service interface":

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 09.14.14.png" alt=""><figcaption></figcaption></figure>

### Seahorse

In Seahorse the passwords are shown in the wallet in a list and are not grouped:

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 10.57.26.png" alt=""><figcaption></figcaption></figure>

You can add new secrets and they can be used by Duplicati.

### KeepSecret

With KeepSecret the passwords created from Duplicati are grouped under "Duplicati Secrets":

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 10.45.36.png" alt=""><figcaption></figcaption></figure>

You can add new secrets and they can be read by Duplicati even if they are not under the "Duplicati Secrets" group.

### KWalletManager

In KWalletManager the secrets are shown in a tree-structure, and for Plasma 6 they show up under "Duplicati Secrets":

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 10.45.11.png" alt=""><figcaption></figcaption></figure>

The UI does not always update when dealing with multiple secrets, so you may need to close and open the wallet for the display to show correctly. You can add secrets here and they can be used in Duplicati even if they are not added under "Duplicati Secrets".

With Plasma 5, there is a compatibility layer that makes the secrets created by Duplicati show up under "Secret Service":

<figure><img src="../../../.gitbook/assets/Screenshot 2026-06-24 at 11.02.43.png" alt=""><figcaption></figcaption></figure>

This is purely cosmetic and they will show up under "Duplicati Secrets" if you upgrade to Plasma 6 or later.

## Using the `pass` secret provider (Linux)

The [`pass` command](https://www.passwordstore.org) is a project that implements a secure password storage solution on Linux system, backed by GPG. Duplicati can use `pass` as the secret provider:

```
--secret-provider=pass://
```

If you want to use `pass`, make sure it is installed on the system. You also need a GPG key, and you can create one with:

```
gpg --full-generate-key
```

As part of the key generation process, you are asked to enter an email address that will later be used to identify the key. Once you have the GPG key you can initialize pass with:

```
pass init <your-email-address>
```

## Using the KeyChain (MacOS)

For MacOS users the standard password storage is the [KeyChain Access](https://support.apple.com/lt-lt/guide/keychain-access/kyca1083/mac) program. The secrets stored here as application passwords can be used by Duplicati. The KeyChain can be enabled as a secret provider with:

```
--secret-provider=keychain://
```

For more advanced uses the options `account` and `service` can be used to narrow down what secrets can be extracted.
