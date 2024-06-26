# Bootstrap
Script to automate the bootstrap creation for Verus, Verus Testnet and (near future) PBaaS chains. Likely usable for most BTC or Zcash descendant chains.

This script is currently in use to produce the Verus bootstrap and is tested on Devuan BeoWulf, Debian Bullseye, Debian Bookworm  and Ubuntu Focal Fossa.
Tested chains: VRSC, vARRR, vrsctest.
Tested daemon: Up till verus release v1.2.2-5

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
For stand-alone operations:
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
For dployment on multiple webservers:
 - Passwordless ssh access to the remote webserver (SSH-key and configured in `~/.ssh/config`)
 - An account that has read/write access to the webroot of the remote server

## Configuration
The `<CoinTicker>.json` must be present in the same folder as the `bootstrap` script. You need to  either copy and adapt the examples in the `json-examples` directory or create your own
`<CoinTicker>.json` and fill with the required information. The entered values are case sensitive and paths are absolute paths.
Values in the `CoinTicker>.json`:
```json
{
  "coin": "VRSC",                                                 // (Mandatory) The genarlly accepted cointicker
  "coin_name": "Verus",                                           // (Mandatory) The name for the chain
  "daemon_user": "verus",                                         // (Mandatory) The linux user that is running the coin daemon
  "daemon": "/home/verus/bin/verusd",                             // (Mandatory) The complete path to (and including) the coindeamon, DO NOT ADD ANY PARAMETERS
  "rpc_client": "/home/verus/bin/verus",                          // (Mandatory) The complete path to (and including) the RPC client, DO NOT ADD ANY PARAMETERS
  "custom_chain_folder": true|false,                              // (Mandatory, Boolean)   
  "chain_folder": "/home/verus/.komodo/VRSC",                     // (Mandatory) The complate path to the chain data
  "template_folder": "/root/bin/bootstrap/bootstrap.template",    // (Mandatory) The complete path to the template for the website
  "staging_folder": "/tmp/bootstrap",                             // (Mandatory) The complete path to the temporary staging folder
  "web_root": "/var/www/html",                                    // (Mandatory) The complete path to the webroot of the webserver
  "fork_check": true,                                             // (Boolean)   If true do external checks to the link supplied in "explorer_url"
  "explorer_url": "https://explorer.verus.io/api",                // (Mandatory when "fork_check": true) URL to the external blockchain explorer, used for fork checking and block- and hash-links on the site.
  "sign": true,                                                   // (Boolean)   Whether to sign the archive with a VerusID
  "signee": "Verus Coin Foundation Bootstrap@",                   // (Mandatory when "sign": true) The ID on the chain to sign
  "reindex": false,                                               // (Boolean)   When true, the chain will reindex after bootstrap creation.
                                                                  //             This value is overridden by "resync": true
  "resync": true,                                                 // (Boolean)   Synchronize the chain from genesis after bootstrap generation
  "resync_day": "daily",                                          // (integer or text) Text "daily" will trigger a resync on any weekday
                                                                  //                   Number 0-6 will trigger the resync ONLY on that weekday
                                                                  //                   0=Sunday, 1=Monday, ... , 6=Saturday
  "distribution_type": "both",                                    // (String)    "zip", "tar" or "both". defaults to "tar" if not present.
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
  ],
  "deploy_externally": false,                                     // (Mandatory, boolean) Deploy to external server(s)
  "deploy_locations":[                                            // (Mandatory when "deploy_externally" is true) "external_location" and "external_web_root" pairs for each external location to deploy to.
    {
      "host": "bootstrap",
      "web_root": "/var/www"
    }
  ]
}
```
The bootstrap webpage will be in a generated <COIN>-bootstrap folder in the supplied webroot
Supply a `<CoinTicker>.png` in the `img` folder for a coin logo on the page.

NOTE: to run the the bootstrap on a cronjob **and** distribute to multiple servers, the server running the bootstrap script must have passwordless (and by extention 2FA-less) access to the target host(s). This is achieved by:
1. Allowing the server access to port 22 (or any other port if you don't use the standard SSH port) on the target server. Preferably only the IP(s) accessing the hosting server(2) are granted access.
2. The public SSH key must be in the `/root/.ssh/authorized_keys` file.
3. Target server access must be properly configured in the `/root/.ssh/config` file.

## Usage:
`./bootstrap <Cointicker>`
The script must be run as `root`, or as another account that can run commands as the user account which runs the coin daemon(s), without a password.
If no command line argument is entered, the script will exit.

## ToDo:
 - Add testnet compatibility.
 - Add complete notarization data for PBaaS chains.
 - Make new templates in line with the Verus website style.
 - Whatever else springs to mind.


## Changes:
Check the https://github.com/Oink70/Bootstrap/blob/main/CHANGELOG.md for changes.

## DISCLAIMER
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notices and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
