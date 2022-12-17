## Aleo
[Website](https://www.aleo.org/post/announcing-testnet-3) | [Discord](https://discord.gg/aleohq) | [Github](https://github.com/AleoHQ/snarkOS/) 
| --- | --- | ---
 
![image](https://user-images.githubusercontent.com/57448493/201538555-429fed0a-7950-4c4e-9e97-c6f368708b5d.png)
 
Aleo has raised $228 million to develop its project, written in its own programming language, LEO. The team aims to create a user interface on the Internet that is both truly private and truly private. With the ability to deploy applications on the network with zero knowledge, which gives security and mitigate leaks of private data on the network. This is just a little bit of what Aleo is all about. 

I advise you to read: https://www.aleo.org/post/discover-aleo


#### 1. Overview
snarkOS is a decentralized operating system for zero-knowledge applications. This code forms the basis of the Aleo network, which verifies transactions and stores encrypted government applications in a public way.

#### 2. Build Guide
2.1 Requirements

Below are the minimum requirements to start an Aleo node:
```Shell
CPU: 16-cores (32-cores preferred)
RAM: 16GB of memory (32GB preferred)
Storage: 128GB of disk space
Network: 10 Mbps of upload and download bandwidth
```
Please note that to run a competitive Aleo Prover machine will require more than these requirements.

#### 2.2 Installation
Before you start, please make sure that you have Rust v1.64+ installed on your computer. Instructions on how to install Rust can be found here.https://www.rust-lang.org/tools/install

Start by cloning this Github repository:
```Shell
git clone https://github.com/AleoHQ/snarkOS.git --depth 1
```
Then go to the snarkOS directory:
```Shell
cd snarkOS
```
[Для пользователей Ubuntu] An auxiliary script for installing dependencies is available. From the snarkOS directory, run:
```Shell
./build_ubuntu.sh
```
Finally, install snarkOS:
```Shell
cargo install --path /root/snarkOS/
```
#### 3. Run an Aleo Node
3a. Start the Aleo client
Start by following the instructions in the build guide.

Then, to start the client node from the snarkOS directory, start:
```Shell
./run-client.sh
```
#### 3b. Run an Aleo Prover
Start by following the instructions in the build guide.

Then generate an Aleo account address:
```Shell
snarkos account new 
```
This will create a new Aleo account in the terminal.

Please remember to save the account private key and the view key. Below is an example output:

 Attention - Remember to store this account private key and view key.
```Shell
  Private Key  APrivateKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me And Use In The Next Step
     View Key  AViewKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me
      Address  aleo1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  <-- Save Me
```     
Then, to start the test node, from the snarkOS directory run:
```Shell
screen -S aleo
./run-prover.sh
```
When prompted, enter your Aleo private key:
```Shell
Enter the Aleo Prover account private key:
APrivateKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

```Shell
snarkos --help
```
```
OPTIONS:
    -h, --help                     Print help information
    -v, --verbosity <VERBOSITY>    Specify the verbosity [options: 0, 1, 2, 3] [default: 2]

SUBCOMMANDS:
    account  Commands for managing Aleo accounts
    clean    Clears the storage of the snarkOS node
    help     Prints this message or help from this subcommand(s)
    start    Starts the snarkOS host
    update   Updates snarkOS
```
