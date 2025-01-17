# Migrating from v2.0 to v2.1

Upgrading from `v2.0` to `v2.1` is fairly straightforward if you follow the instructions. Even though we try to ensure backward compatibility (BC) as much as possible, sometimes it is not possible or very complicated to avoid it and still create a good solution to a problem.

Upgrading, in general, is as simple as updating your installation through the commander or by
running `git pull && yarn setup` inside the installation directory. In a big application, however, there may
be more things to consider, which are explained in the following.

::: tip
This guide assumes you have Core installed inside the `~/solar-core` directory with your configuration being located at `~/.solar`. If you are using different locations, you will need to adjust those inside the examples which can be found below.

Upgrading a complex software project always comes at the risk of breaking something, so make sure you have a backup.
:::

### Notes

It is recommended to start your relay and forger through the [Core Commander](https://github.com/solar-network/core-commander). If you wish to run them on your own, you should take a look at how commander executes them via `pm2`.

After upgrading you should check whether your application still works as expected and no plugins are broken. See the following notes on which changes to consider when upgrading from one version to another.

## Upgrade Steps

### Upgrade Script

You can either run `bash <(curl -s https://raw.githubusercontent.com/Solar/solar-core/master/upgrade/2.1.0/normal.sh)` or run below commands manually.

#### Relay Runners & Delegates

```bash
#!/usr/bin/env bash

cd ~/solar-core
pm2 delete all
git reset --hard
git pull
git checkout master
yarn run bootstrap
yarn run upgrade

# If You Do Not Use Core Commander You Can Skip This Step.
cd ~/core-commander
git reset --hard
git pull
git checkout master
bash commander.sh
```

#### Exchanges

You can either run `bash <(curl -s https://raw.githubusercontent.com/Solar/solar-core/master/upgrade/2.1.0/exchange.sh)` or run below commands manually.

```bash
#!/usr/bin/env bash

cd ~/solar-core
pm2 delete solar-core
pm2 delete solar-core-relay
git reset --hard
git pull
git checkout master
yarn run bootstrap
yarn run upgrade

pm2 --name 'solar-core-relay' start ~/solar-core/packages/core/dist/index.js -- relay --network mainnet
```

#### Notes

- The `yarn setup` command will upgrade Core and its direct dependencies. Without `yarn setup` the upgrade will fail as Core is written in TypeScript and the files need to be run through the TypeScript Compiler **(tsc)**.

### Changes

#### Configuration

- If you have been using custom dynamic fees, open the `~/.config/solar-core/<network>/plugins.js` file and locate the `@solar-network/core-transaction-pool` plugin. Add below code to it and enter your desired values.

```js
dynamicFees: {
    enabled: true,
    minFeePool: 3000,
    minFeeBroadcast: 3000,
    addonBytes: {
        transfer: 100,
        secondSignature: 250,
        delegateRegistration: 400000,
        vote: 100,
        multiSignature: 500,
        ipfs: 250,
        timelockTransfer: 500,
        multiPayment: 500,
        delegateResignation: 400000,
    },
},
```

#### Path

From version 2.1 onwards Core will make use of system specific paths to support as many platforms as possible in the long-term.

| Old             | New                                  |
| :-------------- | :----------------------------------- |
| ~/.solar/database | $HOME/.local/share/solar-core/$network |
| ~/.solar/config   | $HOME/.config/solar-core/$network      |
| n/a             | $HOME/.cache/solar-core/$network       |
| ~/.solar/logs     | $HOME/.local/state/solar-core/$network |
| n/a             | /tmp/$USER/solar-core/$network         |
| ~/.solar/.env     | $HOME/.config/solar-core/$network/.env |

You can read more about those paths at [https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html).

### Conclusion

Once all these changes have been made you will need to restart your relay and forger _(if you are a delegate)_ for these changes to take effect.

If you've been running your relay and forger manually you need to change `packages/core/bin/solar` to `packages/core/dist/index.js` to ensure that the JavaScript files, created by the TypeScript Compiler, are executed.

_Also make sure that you are no longer passing in the `--data` and `--config` flags so core can pick up the system paths. Those flags are only intended for development or custom setups._
