# Bootstrap
Script to automate the bootstrap creation for Verus, Verus Testnet and (future) PBaaS chains

## Description:
This script creates bootstrap archives from the locally running chain in tar.gz and zip format.

After the archives are made, the MD5, SHA256 and SHA512 checksums are calculated.

Of the archives a Verus Signature will optionally be created.

It creates a basic page, based on the customizable `bootstrap.html.template` with this data:
 - Cointicker and coin name.
 - Links to the project (specified in the `<COINTICKER>.json`)
 - Bootstrap data like highest block timestamp, blockheight and best hash.
 - The bootstrap archives, their checksums and (optional) signature.

After staging the page, it will move the current bootstrap web folder to `last.<COINTICKER>-bootstrap`, and the staging folder will be renamed to `<COINTICKER>-bootstrap` in the web root location supplied in the json for the coin.

## Prerequisites:
 - Access to the `root` account.
 - A running, fully synchronized `verusd` daemon, started with at least the `-datadir=<PATH>` option (required to differentiate between multiple daemons).
 - A configured `<COINTICKER>.json` file in the scripts folder. All paths used must be absolute paths.
 - The VerusID signing the bootstrap must be in the local wallet for the chain and have ability to transact.
 - `jq` installed.
 - `zip` installed.
 - `CURL` installed.
 - `BC` installed.
 - `TR` installed.

 - A running webserver to host the webpage and bootstrap files.

## Usage:
`./bootstrap <Cointicker>`

The script must be run as `root`, or as another account that can run commands as the user account which runs the coin daemon(s), without a password.

The `<CoinTicker>.json` must be present in the same folder as the `bootstrap` script. Configure your own `<CoinTicker>.json` and fill with the required information. The entered values are case sensitive and paths are absolute paths.

Supply a `<CoinTicker>.png` in the `img` folder for a logo on the page.
If no command line argument is entered, the script will exit.

## ToDo:
 - Add in option to do a genesis sync periodically.
 - Continued testing on VRSCTEST PBaaS chains.
 - Make new templates in line with the Verus website style.
 - Whatever else springs to mind.


## Changes:
 - 2021-10-29; Make external checks optional for every coin.
 - 2021-10-29; Added explorer link to the json files, to allow checking chain validity against an external source, before proceeding.
 - 2021-10-29; Added further sanity checks.
 - 2021-10-29; removed execution permissions from files that are not executable.
 - 2021-10-15; Check if chain is running at start of script. If not running, the script will exit.
 - 2021-10-15; Check hash of local bestblock against hash of same block on explorer.verus.io (checking on fork). Script will exit if not identical (VRSC main chain only)
 - 2021-10-15; Check blockheight against explorer.verus.io and wait until the local daemon is synchronized with the explorer. (VSC main chain only)
 - 2021-06-09; Auto-remove distribution archive from `last.$COIN-bootstrap` folder to conserve storage space.
 - 2021-06-08; Signatures and links to them are removed from webpage if signing fails.
 - 2021-06-08; Made optional archive for distribution through value in `<COINTICKER>.json`. Archive is placed in the bootstrap web folder for easy downloading.
 - 2021-06-08; Made signed releases optional through value in `<COINTICKER>.json`.
 - 2021-06-08; Made reindexing optional through value in `<COINTICKER>.json`.
 - 2021-06-08; Added external dependency check for `zip`.
 - 2021-06-07; Fill the website with links from the `<COINTICKER>.json`.
 - 2021-06-07; Check if the chain is completely synchronized and wait for full synchronization if needed (no fork check).
 - 2021-06-07; Fixed shutdown check on specific daemon.
 - 2021-06-05; Implemented a check on shutdown of the specific daemon.
 - 2021-06-05; Implemented dependencies check
 - 2021-06-05; Bootstrap time no longer shows the time the bootstrap process is actually started, but reflects the time the last included block was mined/staked.
 - 2021-05-24; added `-rf` to `rm last.-bootstrap`
 - 2021-05-18; Add `-datadir` to daemon calls
 - 2021-05-17; Initial commit
