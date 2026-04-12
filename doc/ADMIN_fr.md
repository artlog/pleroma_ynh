# Pleroma et Yunohost

Quelques informations, à toutes fins utiles, sur les différences entre une installation directe de Pleroma (depuis les _releases_ du dépôt officiel) et l’installation avec le package d’application de Yunohost.

Il existe deux possibilités pour obtenir une installation pleroma depuis le upstream : MIX et OTP
MIX depuis les sources requiert une compilation mais utilise les dépendances du système, c'est à dire les paquets elixir et erlang.
Si une version 'OTP' est disponible depuis le projet pleroma, il est fourni avec les binaires elixir et erland et des outils spécifiques OTP.


| MIX                         | Official Package               | With Yunohost                           |
| --------------------------- | ------------------------------ | --------------------------------------- |
| Install directory           | `/opt/pleroma`                 | `/var/www/pleroma/mix/`                 |
| Static directory            | `instance/static`              | `/home/yunohost.app/pleroma/`           |
| Configuration file          | `config/${MIX_ENV}.secret.exs` | `/etc/pleroma/config.exs`               |
| Command line path           | `mix`                          | `mix` with pleroma HOME (see examples)  |

## À noter

- This installation of Pleroma with Yunohost is MIX.  i.e. from the sources. This is slower since compilation occurs at install.
- Les installation précédentes de Pleroma avec Yunohost étaient des installations dite OTP (et non MIX, c’est à dire à partir des sources). Information à mémoriser, dans la mesure où les commandes d’administration ne sont pas les mêmes dans l’un ou l’autre cas.
- Les très anciennes installation ( < 2020 ) étaient en MIX

Cela a changé au gré de la disponiblité des build OTP depuis le projet parent pleroma, donc d'un version à l'autre de yunhost cela peut changer.

Il faut s'en souvenir car les commandes à utiliser ne sont pas les même dans le cas MIX ou dans le cas OTP.

- Toutes les commandes doivent être exécutées en tant qu’utilisateur `pleroma`, qui n’est _pas_ membre du groupe `admin` de Yunohost, celle-ci ne peuvent donc être invoquées directement avec `sudo`.

## Tâches d’administration

Se connecter avec SSH à votre serveur Yunohost avec le compte admin (nous parlons ici de l’admin YNH pas de l’admin de Pleroma).
Pour la syntaxe des commandes, garder en tête les spécificités d’une installations de [Pleroma avec Yunohost](./doc/yunohost_fr.md).

### MIX

MIX was done to test compile and run, this comes with some constraints, usualy mix should be run in project directory and have a MIX_ENV set.
It is usual in MIX to rely on HOME being same as project directory, what then explains following commands.

#### Ajouter un utilisateur ou utilisatrice

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user new <userName> <userEmail>"
```

#### Changer de mot de passe

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user reset_password <userName>"
```

This will generate a **password reset link** that you can then send to the user.

#### Modérateurs

Vous pouvez donner à des utilisateurs ou utilisatrices les droits de **modération**. Ils ou elles pourront alors _supprimer_ n’importe quel billet publié par n’importe quel autre compte.

```bash
HOME=/var/www/pleroma/mix;cd $HOME; sudo -u pleroma HOME=$HOME MIX_ENV=prod /usr/bin/mix pleroma.user set <userName> --admin"
```

### OTP

|                             | Package officiel           | Avec Yunohost                           |
| --------------------------- | -------------------------- | --------------------------------------- |
| Répertoire d’installation   | `/opt/pleroma`              | `/var/www/pleroma/live/`                 |
| Répertoire statique         | `/var/lib/pleroma/static`   | `/home/yunohost.app/pleroma/`                |
| Fichier de configuration    | `/etc/pleroma/config.exs`   | `/etc/pleroma/config.exs`                |
| Ligne de commandes          | `/bin/pleroma_ctl`         | `/var/www/pleroma/live/bin/pleroma_ctl`  |

#### Ajouter un utilisateur ou utilisatrice

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user new <userName> <userEmail>"
```

#### Changer de mot de passe

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user reset_password <userName>"
```

Ceci générera un **lien de réinitialisation** (URL) du mot de passe, que vous pouvez envoyer à l’utilisateur ou utilisatrice.

#### Modérateurs

Vous pouvez donner à des utilisateurs ou utilisatrices les droits de **modération**. Ils ou elles pourront alors _supprimer_ n’importe quel billet publié par n’importe quel autre compte.

```bash
sudo su pleroma -s $SHELL -lc "/var/www/pleroma/live/bin/pleroma_ctl user set <userName> --admin"
```

Note : l’option `--admin` donne au compte _les droits de moderation_ et avec l’option `--no-admin` à l’inverse on _enlève_ les privilèges de modération de celui-ci.
