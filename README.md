# Bootstrap
Script to automate the bootstrap creation for Verus, Verus Testnet and (future) PBaaS chains

## Description:
This script creates bootstrap archives from the locally running chain in tar.gz and zip format.

After the archives are made, the MD5, SHA256 and SHA512 checksums are calculated.

Of the archives a Verus Signature will be created (Still need to make that optional, for now it's mandatory).

It creates a basic page, based on the customizable `bootstrap.html.template` with this data:
 - Cointicker and coin name
 - Basic links to the project (fixed to Verus now, needs work for future projects)
 - Bootstrap data like creation timestamp, blockheight and best hash
 - The bootstrap archives, their checksums and signature

After staging the page, it will move the current bootstrap webfolder to `last.<cointicker>-bootstrap`, and the staging folder will be renamed to `<cointicker>-bootstrap` in the web root location supplied in the json for the coin.

## Usage:
`./bootstrap <Cointicker>`

The script must be run as `root`, or as another account that can run commands as the useraccount that runs the coin daemon(s) without a password.

The `<CoinTicker>.json` must be present in the same folder as the`bootstrap` script. Configure your own `<CoinTicker>.json` and fill with the required information. The entered values are case sensitive and paths are absolute paths.

Supply a `<CoinTicker>.png` in the `img` folder for a logo on the page.
If no command line argument is entered, the script will exit.

## ToDo:
 - Make VerusID signatures optional.
 - Fill the website with links from the `<CoinTicker>.json`. Links are presently fixed in the template.
 - Implement a check on shutdown of the specific daemon (Possibly checking for `/.komodo/<COIN>/.lock`). Currently 120 seconds wait is used.
 - add an option (commandline or json) to restart the daemon with the `-reindex` parameter at the end, to ensure a consistent chain. This can currently be enforced by editing the script and removing the `#` in fron of lines 115, 116 & 119
 - Whatever elso springs to mind
