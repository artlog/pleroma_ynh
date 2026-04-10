# Pleroma and Yunohost

Some information, for all intents and purposes, on the differences between a direct installation of Pleroma (from the _releases_) and installation with the Yunohost application package.

There are two competing solutions to build and release a pleroma bundle, MIX or OTP.
MIX from source requires a rebuild but uses system dependencies, ie debain packages of elixir and erlang.
If an OTP ( 'Open Telecom Platform' ) version is available from pleroma upstream it comes with its own compiled  version of elixir and erlang.

| MIX                         | Official Package               | With Yunohost                           |
| --------------------------- | ------------------------------ | --------------------------------------- |
| Install directory           | `/opt/pleroma`                 | `/var/www/pleroma/mix/`                 |
| Static directory            | `instance/static`              | `/home/yunohost.app/pleroma/`           |
| Configuration file          | `config/${MIX_ENV}.secret.exs` | `/etc/pleroma/config.exs`               |
| Command line path           | `mix`                          | `mix` with pleroma HOME (see examples)  |

## Things to remember

- This installation of Pleroma with Yunohost is MIX.  i.e. from the sources. This is slower since compilation occurs at install.
- Previous installations of Pleroma with Yunohost was a so-called OTP installation (and not MIX,).
- Very old installation ( < 2020 ) were MIX too.

It did changes depending on availability of OTP build on pleroma upstream, so from one version to another this can still change.

Have this in mind, as the administration _commands_ are not the same in either case.

- All commands must be run as the `pleroma` user, who is _not_ part of Yunohost `admin` group, it’s not possible to invoque these commands directly with `sudo`.

Connect with SSH to your Yunohost server as YNH admin user.

For the commands syntax, have in mind the specificities of [Yunohost Pleroma install](./doc/yunohost.md).

## Administration tasks

Note: `--admin` option will _make the user moderator_ and `--no-admin` will _take away_ the moderator privileges from the user.

### MIX CASE

MIX was done to test compile and run, this comes with some constraints, usualy mix should be run in project directory and have a MIX_ENV set.
It is usual in MIX to rely on HOME being same as project directory, what then explains following commands.

#### Adding users

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user new <userName> <userEmail>"
```

#### Password reset

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user reset_password <userName>"
```

This will generate a **password reset link** that you can then send to the user.

#### Moderators

You can make users **moderators**. They will then be able to _delete any post_.

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user set <userName> --admin"
```

### OTP CASE

Please NOTE this is for information only : this release is not OTP.

| OTP                         | Official Package           | With Yunohost                           |
| --------------------------- | -------------------------- | --------------------------------------- |
| Install directory           | `/opt/pleroma`             | `/var/www/pleroma/mix/`                 |
| Static directory            | `/var/lib/pleroma/static`  | `/home/yunohost.app/pleroma/`           |
| Configuration file          | `/etc/pleroma/config.exs`  | `/etc/pleroma/config.exs`               |
| Command line path           | `/bin/pleroma_ctl`         |                                         |

#### Adding users

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user new <userName> <userEmail>"
```

#### Password reset

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user reset_password <userName>"
```

This will generate a **password reset link** that you can then send to the user.

#### Moderators

You can make users **moderators**. They will then be able to _delete any post_.

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user set <userName> --admin"
```

