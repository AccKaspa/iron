The directory cli is here for reference. 

You need the full release to build and run the cli.

In order to obtain this source for yourself:
```bash
wget https://github.com/kaspanet/rusty-kaspa/archive/refs/tags/v0.15.2.tar.gz
tar -xvf v0.15.2.tar.gz
cd rusty-kaspa-0.15.2/cli
```

Here we shall demonstrate:
1. Building and running the source
2. Injecting a module into kaspa-cli
3. Triggering modules from arbitary source #TODO
4. Integrating Rust cli modules with Javascript Bare Runtime and Pear #TODO

### Getting Started

If you don't have rust and the package manager cargo installed:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs  | sh -s -- -y
. "$HOME/.cargo/env"
```

Let's start off by taking a look at what the source looks like in action

This directory is set up to be ready to build and run

```bash
cd cli
cargo build --release #wait
../target/release/kaspa-cli
```

This will load you into a prompt that looks like
```bash
Kaspa Cli Wallet v0.15.2 (type 'help' for list of commands)
$ 
```

Type help.

Each of these help messages more or less point to a file in the modules directory.

You can know how each and every one of these works if you read carefully enough this src directory.

Now type exit

```bash
bye!
```

### First Tweaks

Our goal is to extract those commands without entering the user typing loop. 

Before we do this we must understand how they work in the current order.

To this end, we seek to add our own module. 

First let us edit the cli in order the change the prompt from $ to something more apt just to see the file where everything comes together at a high level.

```bash
EDIT=vim #nano etc...
$EDIT src/main.rs #replace
```

Change the with_prompt string "$ " to "fast-interactive 🐌: "

Now you will need to rebuild

```bash
cargo build --release #will be faster this time...still wait though
../target/release/kaspa-cli
```

You should now notice the variation you opened up within the source

Do note that to do anything with this we need to connect to the broader network; first we set that network

In order to ensure that we can emanate as much error as we please, let us work on the testnet

Type network testnet-10

Type connect

What this little tool just did was find a public node which keeps track of the synchronized state of the datastructure which represents the agreed upon state of the network. The rust code here uses websockets to connect.

Note that if you wanted to run your own local node

```bash
cd ..
cargo build --release --bin kapsad
screen -dmS kaspad ./target/release/kaspad --utxoindex --rpclisten-borsh=default #wait a few hours and 20GB later
```

The flag for utxoindex ensures that we can handle user wallet functions. Keep in mind that is speaks to unspent transaction outputs.

The flag for rpclisten-borsch ensures that websocket Remote Proceudre Calls are enabled and accessible at the default local server 127.0.0.1.

Then you could 
```bash
./target/release/kaspa-cli
Kaspa Cli Wallet v0.15.2 (type 'help' for list of commands)
fast-interactive 🐌: network testnet-10
fast-interactive 🐌: connect 127.0.0.1
```

Generally, you should find the public connection suitable if you wish to conserve compute resources in your investigations.

### Understanding Modules

Notice that we can find functions for each of the help messages in src/cli.rs

For each of these, there should be a line towards the top of the file "mod module"

The simplest thing that can be done is to ping the node that we are connected to

First let us see it in action

Assuming we are in the cli directory again

```bash
../target/release/kaspa-cli
Kaspa Cli Wallet v0.15.2 (type 'help' for list of commands)
fast-interactive 🐌: network testnet-10
fast-interactive 🐌: connect
fast-interactive 🐌: ping
```

You should see ping ok. Exit now.

Let us change duplicate this file and change the message. We shall venture to call it qping

In order to get qping to be properly stitched into the binary as a whole we must do more than just copy the file in src/modules

Nonetheless, we do begin there:

```bash
cp src/modules/ping.rs src/modules/qping.rs
```

Next we change the file to be named just this

```bash
EDIT=vim #nano etc...
$EDIT src/module/qping.rs
```

1. Change pub struct Ping to pub struct Qping
2. Change impl Ping to impl Qping
3. Change tprintln!(ctx, "ping ok") to tprintln!(ctx, "slime up")
4. Change the error message in the else case to "slime down"
5. Change the help message "Ping the connected node" to "Can we Slime the RPC??"
6. Save and exit the file #ESC:wq for vim; w saves. q quits. : is buffer. ESC gets you to the buffer
   
We want to know if our open snail imagination can slime the remote procedure calls which make the newtork fold to our $FAST energetics.

Before we can put our slime checker in action we need to modify src/modules/mod.rs

```bash
EDIT=vim #nano etc...
$EDIT src/modules/mod.rs
```

1. Go to pub mod ping;
2. Copy this line and add q to ping for qping
3. Go to the fn register_handlers: add qping, after ping
4. Save and Exit the file #ESC:wq
5. Rebuild and Rerun kaspa-cli
```bash
cargo build --release #wait
../target/release/kaspa-cli
```
6. Type help and look for qping #it is between pskb and reload
7. Type qping

You can now deliver arbitray payloads to be invoked from the kaspa-cli

### Calling Modules from Source (Coming Soon)

While it is nice to understand and extend the existing command line interface, we snails endeavor with Iron on Rust to be able to build light and modular tools that open up the source of rusty-kaspa for integration into further projects in a way that feels fresh. We want to bring plug and play source to a newtork that runs faster than we can even conceptualize. Delivering payloads into custom modules for kaspa-cli is a good place to start, but the minimal source needed for maximal conceptual impact is the slime that snails go hyperbolic for. With simplicity we build the complexity of robust networks.
