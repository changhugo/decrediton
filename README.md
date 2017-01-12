# decrediton

decrediton is a cross-platform GUI for decred written in node.js using
Electron.

## Developing

Due to potential compatibility issues, for now, all work should be
done with node v6.5.0 and electron 1.4.6.  It is likely we can relax
this once things have settled down.  The recommended way to install
node is using nvm.

This has primarily been tested on Linux at the moment although OSX
should work similarly.

``` bash
git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
. ~/.nvm/nvm.sh
nvm install v6.5.0
nvm use v6.5.0
nvm alias default v6.5.0
npm install -g npm
cd
```

Due to an incompatibility between grpc and electron, several extra
steps are necessary.  As some current PRs make it into grpc these
steps will be unnecessary in the future.  Since you must locally build
grpc, you must have compilers and the normal development tools
installed.

Adjust the following steps for the paths you want to use.

``` bash
mkdir code
cd code
git clone https://github.com/decred/decrediton.git
git clone https://github.com/grpc/grpc
cd grpc
git remote add murg https://github.com/murgatroid99/grpc.git
git fetch --all
git checkout node_electron_build
git submodule update --init
npm install
npm run electron-build -- --target=1.4.6
cd ../decrediton
npm install
```

Start dcrd and dcrwallet with the following options.  Note, you must
NOT already have rpc certs from dcrwallet so it is easiest to start
with an emtpy $HOME/.dcrwallet

```bash
dcrd --testnet -u USER -P PASSWORD --rpclisten=127.0.0.1:19109 --rpccert=$HOME/.dcrd/rpc.cert
```

```bash
dcrwallet --testnet --experimentalrpclisten=127.0.0.1:19112 --noinitialload --tlscurve=P-256 --onetimetlskey --appdata=~/.decrediton
```

On macOS you should use:
```bash
dcrwallet --testnet --experimentalrpclisten=127.0.0.1:19112 --noinitialload --tlscurve=P-256 --onetimetlskey --appdata=$HOME/Library/Application\ Support/decrediton
```

Start decrediton

```bash
npm run dev
```

## Building the package

To build a packaged version of decrediton (including a dmg on OSX and
exe on Windows), follow the development steps above.  Then build the
dcr command line tools:

```
go get -u -v github.com/decred/dcrd
go get -u -v github.com/decred/dcrwallet
cd $GOPATH/github.com/decred/dcrd
glide i
go install . ./cmd/dcrctl/
cd ../dcrwallet
glide i
go install
cd
cd code/decrediton
mkdir bin
cp `which dcrd` bin/
cp `which dcrctl` bin/
cp `which dcrwallet` bin/
npm install
npm run package
```
At this time cross compiling is not possible.

## Installation

Currently decrediton has only been tested and built on Linux and
macOS.  Additional systems will be added in the future.

Decrediton will NOT use or in any way disrupt the wallet file you may
already be using at this time.

Download the decrediton release for your operating system.

On linux you will need to decompress the package and then run the
file:
```
./decrediton
```

This will start dcrd and dcrwallet for you.

On macOS, double-click the .dmg file, drag the .app to your
Applications folder.  Double click on Decrediton.app to start.

From there follow the on screen instructions to setup your wallet.

## Contact

If you have any further questions you can find us at:

- irc.freenode.net (channel #decred)
- [webchat](https://webchat.freenode.net/?channels=decred)
- forum.decred.org
- decred.slack.com

## Issue Tracker

The
[integrated github issue tracker](https://github.com/decred/decrediton/issues)
is used for this project.

## License

decrediton is licensed under the [copyfree](http://copyfree.org) ISC License.

