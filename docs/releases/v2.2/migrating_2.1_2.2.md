# Migrating from v2.1 to v2.2

Upgrading from `v2.1` to `v2.2` is fairly straightforward if you follow the instructions. Even though we try to ensure backward compatibility (BC) as much as possible, sometimes it is not possible or very complicated to avoid it and still create a good solution to a problem.

::: warning
Upgrading a complex software project always comes at the risk of breaking something, so make sure you have a backup.
:::

### Notes

After upgrading you should check whether your application still works as expected and no plugins are broken. See the following notes on which changes to consider when upgrading from one version to another.

## Prerequisites

::: warning
Be sure to complete all of the following changes before you continue to upgrade to the latest version.
:::

### Configuration

- Since 2.2 we no longer ship `@solar-network/core-graphql` by default, open the `~/.config/solar-core/<network>/plugins.js` file (e.g. for mainnet using nano you would run `nano ~/.config/solar-core/mainnet/plugins.js`), locate the `@solar-network/core-graphql` plugin and remove the whole block.

```js
"@solar-network/core-graphql": {
    enabled: process.env.CORE_GRAPHQL_ENABLED,
    host: process.env.CORE_GRAPHQL_HOST || "0.0.0.0",
    port: process.env.CORE_GRAPHQL_PORT || 4005,
},
```

> If you are using the plugin and want to continue using it you need to run `yarn global add @solar-network/core-graphql` and leave your configuration unchanged.

## Upgrade Steps

::: warning
Do not run any of the mentioned commands with `sudo` unless explicitly stated.
:::

### Installing 2.2.0

```bash
pm2 delete all
yarn global add @solar-network/core
echo 'export PATH=$(yarn global bin):$PATH' >> ~/.bashrc
export PATH=$(yarn global bin):$PATH
```

::: tip
If you experience any issues with `yarn` after this or see a message like `Command 'solar' not found` a simple log out and log in should help.
:::

### Start Relay

```bash
solar relay:start
```

### Start Forger

```bash
solar forger:start
```

### Removing 2.1.0

```bash
rm -rf ~/core
rm -rf ~/solar-core
rm -rf ~/core-commander
```

## Reporting Problems

Due to 2.2 being distributed and managed in a completely different way than 2.1 there might be cases where unexpected issues show up.

If you happen to experience any issues please [open an issue](https://github.com/solar-network/solar-core/issues/new?template=Bug_report.md) with a detailed description of the problem, steps to reproduce it and info about your environment.
