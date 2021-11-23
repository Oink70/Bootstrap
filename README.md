# Bootstrap
Script to automate the bootstrap creation for Verus, Verus Testnet and (near future) PBaaS chains. Likely usable for most BTC or Zcash descendant chains.

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

## Configuration
The `<CoinTicker>.json` must be present in the same folder as the `bootstrap` script. Configure your own `<CoinTicker>.json` and fill with the required information. The entered values are case sensitive and paths are absolute paths.
Values in the json:
```json
{
  "coin": "VRSC",                                                 // (Mandatory) The genarlly accepted cointicker
  "coin_name": "Verus",                                           // (Mandatory) The name for the chain
  "daemon_user": "verus",                                         // (Mandatory) The linux user that is running the coin daemon
  "daemon": "/home/verus/bin/verusd",                             // (Mandatory) The complete path to (and including) the coindeamon, DO NOT ADD ANY PARAMETERS
  "rpc_client": "/home/verus/bin/verus",                          // (Mandatory) The complete path to (and including) the RPC client, DO NOT ADD ANY PARAMETERS
  "chain_folder": "/home/verus/.komodo/VRSC",                     // (Mandatory) The complate path to the chain data
  "template_folder": "/root/bin/bootstrap/bootstrap.template",    // (Mandatory) The complete path to the template for the website
  "staging_folder": "/tmp/bootstrap",                             // (Mandatory) The complete path to the temporary staging folder
  "web_root": "/var/www/html",                                    // (Mandatory) The complete path to the webroot of the webserver
  "fork_check": true,                                             // (Boolean)   If true do external checks to the link supplied in "explorer_api"
  "explorer_api": "https://explorer.verus.io/api",                // (Mandatory when "fork_check": true) URL to the external API endpoint
  "sign": true,                                                   // (Boolean)   Whether to sign the archive with a VerusID
  "signee": "Verus Coin Foundation Bootstrap@",                   // (Mandatory when "sign": true) The ID on the chain to sign
  "reindex": false,                                               // (Boolean)   When true, the chain will reindex after bootstrap creation.
                                                                  //             This value is overridden by "resync": true
  "resync": true,                                                 // (Boolean)   Synchronize the chain from genesis after bootstrap generation
  "resync_day": "daily",                                          // (integer or text) Text "daily" will trigger a resync on any weekday
                                                                  //                   Number 0-6 will trigger the resync ONLY on that weekday
                                                                  //                   0=Sunday, 1=Monday, ... , 6=Saturday
  "archive": false,                                               // (Boolean)   After finishing bootstrap, create a "COIN-bootstrap.tar" in the standard webfolder
  "links":[                                                       // Optional:   "tag" and "URL" pairs included in the bootstrap webpage
    {
      "tag": "Website",
      "URL": "https://verus.io"
    },
    {
      "tag": "Github",
      "URL": "https://github.com/veruscoin"
    },
    {
      "tag": "Bitcointalk",
      "URL": "https://bitcointalk.org/index.php?topic=4070404.0"
    },
    {
      "tag": "Discord",
      "URL": "https://verus.io/discord"
    },
    {
      "tag": "Explorer",
      "URL": "https://explorer.verus.io"
    }
  ]

}

```
The bootstrap webpage will be in a generated <COIN>-bootstrap folder in the supplied webroot
Supply a `<CoinTicker>.png` in the `img` folder for a coin logo on the page.

## Usage:
`./bootstrap <Cointicker>`
The script must be run as `root`, or as another account that can run commands as the user account which runs the coin daemon(s), without a password.
If no command line argument is entered, the script will exit.

## ToDo:
 - Add option to verify the last x blocks if reindex and resync is inactive.
 - Add info on webpage on *Genesis synchronized*, *Reindexed*, or *x blocks checked*
 - Continued testing on VRSCTEST PBaaS chains (current version is only thoroughly tested at mainnet).
 - Make new templates in line with the Verus website style.
 - Make zip and tarball optional, defaulting to tarball.
 - Whatever else springs to mind.


## Changes:
 - 2021-11-22; Added explanation concerning the values in the `<COINTICKER>.json` in the README.md
 - 2021-11-22; Added daily genesis synchronization capabilities.
 - 2021-11-20; Added checks on the validity of the data specified in the `<COIN>.json` to ensure that files, folders and user exist.
 - 2021-11-20; Refactored the `su` commands, making then uniform accross platforms.
 - 2021-11-20; added extra fields in the example`VRSC.json` to accommodate a *ReSync* option.
 - 2021-11-20; Added the option to do a weekly Resync from genesis on a specific day (0=Sunday... 6=Saturday, or "daily" for every day). This option will override the Reindex option for that day.
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
