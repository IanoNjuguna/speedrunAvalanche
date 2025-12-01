# Day 0

Deploy & Verify a smart contract on the Avalanche Fuji testnet using Foundry.

## Prerequisites
----
* Basic command line knowledge
* A wallet with a private key
* Get [test $AVAX](https://core.app/tools/testnet-faucet/)

## Environment Setup

1. Create Project Directory:
``` bash
mkdir 00-setup && cd 00-setup
```

2. Install Foundry
```bash
curl -L https://foundry.paradigm.xyz | bash
foundryup
```
This installs Foundry and updates it to the latest version.

3. Initialize Foundry Project
```bash
forge init
```

> **Warning**

> If the current directory is not empty, Foundry will refuse to initialize and instead generates this error:
<span style="color:red;">
	Cannot run `init` on a non-empty directory.
	Run with the `--force` flag to initialize regardless.
</span>


> You have two safe options:
>
> I. Create and enter a new empty directory:
```bash
	mkdir new_dir && cd new_dir
	forge init
```
> II. Force initialization. This will overwrite all existing project files. Use only if you've got your existing files backed up:
```bash
	forge init --force
```

Your project has the following structure:

- `src/` - Smart contracts
- `test/` - Test files
- `script/` - Deployment scripts
- `foundry.toml` - Configuration file

---
## Configure Your Environment

1. Create .env file in the root of the repository:
```bash
touch .env
```

2. Add Network Configuration:

```bash
AVALANCHE_RPC_URL="https://api.avax.network/ext/bc/C/rpc"
AVALANCHE_FUJI_URL="https://api.avax-test.network/ext/bc/C/rpc"
ETHERSCAN_API_KEY="your-api-key-here"

**Important:**
Replace `your-api-key-here` with your actual Etherscan API key.

3. Load Environment Variables
```bash
source .env
```

You need to run `source .env` every time you open a new terminal session, or after modifying the new `.env` file.

4. Secure your private key
```bash
cast wallet import deployer --interactive
```

When prompted:
* enter your private key

* create a password

Your private key is stored in `~/.foundry/keystores`. It is not tracked by git. Never share or commit your private key.

## Deploy Your Contract

1. Compile all the contracts in the `src/` directory:
``` bash
forge build
```

2. Deploy to the Fuji Testnet
``` bash
forge create ./src/Counter.sol:Counter --rpc-url https://api.avax-test.network/ext/bc/C/rpc --account deployer --broadcast
```

**Important:**
- The `broadcast` flag is **required** to actually deploy. Without it, you only get a dry-run simulation
- You'll be prompted to enter your keystore password.
- The keystore password is the password you chose when you created or imported the encrypted keystore. Foundry cannot recover it for you.

- The format is `<contract-path>:<contract-name>`

Expected output:
```
Deployer: 0x...
Deployed to: 0x... <-- YOUR CONTRACT ADDRESS
Transaction hash: 0x...
```

3. Save Your Contract Address

- Copy the `Deployed to:` address and add it to your `.env` file:

```bash
echo 'CONTRACT_ADDRESS="0xYourDeployedAddressHere"' >> .env
```

- Replace `0xYourDeployedAddressHere` with your actual deployed address.

4. Reload Environment variables
```bash
source .env
```


5. Copy the contract address and verify it on [Testnet Snowscan](https://testnet.snowscan.xyz/)

## Interact With Your Contract

1. Read Contract State, i.e view function:

```bash
cast call $CONTRACT_ADDRESS "number()(uint256)" --rpc-url $AVALANCHE_FUJI_RPC_URL
```

Expected output:
`0`

**Note:** `cast call` doesn't require signing because it is a read-only operation.

2. Write to Contract

Increment the counter by 1:
```bash
cast send $CONTRACT_ADDRESS "increment()" --rpc-url $AVALANCHE_FUJI_RPC_URL --account deployer
```

You'll need to enter your keystore password.

Expected output includes:
```txt
status               1 (success)
transactionHash      0x...
```

- `status: 1` = Success
- `transactionHash` = Unique ID for your transaction (viewable on the Testnet Snowscan)
- `blobGasUsed` = Gas consumed by the transaction
- `to` = Your contract address
- `from` = Your wallet address

3. Verify the Increment

Read the counter value again:

```bash
cast call $CONTRACT_ADDRESS "number()(uint256)" --rpc-url $AVALANCHE_FUJI_RPC_URL
```

Expected output: `1`

4. Write to Contract, i.e: Set Number

Set the counter to a specific value (e.g., 420):

```bash
cast send $CONTRACT_ADDRESS "setNumber(uint256)" 420 --rpc-url $AVALANCHE_FUJI_RPC_URL --account deployer
```

5. Verify the New Value

```bash
cast call $CONTRACT_ADDRESS "number()(uint256)" --rpc-url $AVALANCHE_FUJI_RPC_URL
```

Expected output: `420`

## Understand the Counter Contract

The counter contract (`src/Counter.sol`) has three functions:

1. **`number()`** - view function that returns the current counter value
2. **`increment()`** - increases the counter by 1
3. **`setNumber(uint256 newNumber)`** - sets the counter to a specific value



## Resources

- [Based East Africa Builder Bootcamp](https://github.com/Based-East-Africa/Base-Batches-002/)
- [Foundry Book](https://book.getfoundry.sh/)
- [Avalanche Fuji Faucet](https://core.app/tools/testnet-faucet/)
- [Testnet Snowscan](https://testnet.snowscan.xyz/)
- [Solidity Documentation](https://docs.soliditylang.org/)