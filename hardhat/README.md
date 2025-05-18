# Hardhat Deployment Example for 0G Testnet V3

This directory contains an example of deploying an ERC20 token (`MyToken.sol`) to the 0G Testnet V3 (Chain ID: 16601) using Hardhat.

## Prerequisites

*   Node.js and npm
*   Hardhat 

## Setup

1.  Initialize a new Hardhat project:

    ```bash
    npm init -y
    npm install --save-dev hardhat
    npx hardhat init
    ```
    Install OpenZeppelin contracts:
    ```bash
    npm install @openzeppelin/contracts
    ```
    Create your contract:
    In the contracts folder, create a new Solidity file, e.g., **MyToken.sol**:
    ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.20;

        import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
        import "@openzeppelin/contracts/access/Ownable.sol";

        contract MyToken is ERC20, Ownable {
        constructor(uint256 initialSupply) 
        ERC20("Tempe Tahu", "TT") 
        Ownable(msg.sender)
        {
        _mint(msg.sender, initialSupply * 10 ** decimals());
        }

        function mint(address to, uint256 amount) public onlyOwner {
            _mint(to, amount);
        }
    } 
    ```
    Edit **hardhat.config.js** to set up your network and compiler:
    
    ```javascript
    require("@nomicfoundation/hardhat-toolbox");
    require("dotenv").config();

    const privateKey = process.env.PRIVATE_KEY;
    const rpcUrl = process.env.RPC_URL;

    if (!privateKey) {
      console.warn("Please set your PRIVATE_KEY in the .env file");
    }
    if (!rpcUrl) {
      console.warn("Please set your RPC_URL in the .env file");
    }

    /** @type import('hardhat/config').HardhatUserConfig */
    module.exports = {
      solidity: "0.8.24",
      networks: {
        hardhat: {
          // Configuration for the local Hardhat Network
        },
        og_testnet_v3: {
          url: rpcUrl || "", // Fallback to empty string if not set
          accounts: privateKey ? [`0x${privateKey}`] : [], // Add 0x prefix
          chainId: 16601, // Updated Chain ID for 0G Testnet V3
        },
      },
      // Optional: Specify etherscan API key if verification is needed later
      // etherscan: {
      //   apiKey: process.env.ETHERSCAN_API_KEY
      // }
    };
    ```
    In the scripts folder, create **deploy.js**:
    
    ```javascript
    const hre = require("hardhat");

    async function main() {
      const initialSupply = hre.ethers.parseUnits("1000000", 18); // 1,000,000 tokens with 18 decimals

      console.log("Deploying MyToken contract...");

      const MyToken = await hre.ethers.getContractFactory("MyToken");
      const myToken = await MyToken.deploy(initialSupply);

      // No need to wait for deployment explicitly with Hardhat V6 and ethers v6
      // The deploy function returns a promise that resolves once the contract is deployed.
      // However, getting the address requires the deployment transaction to be mined.
      // Use Contract.waitForDeployment() if you need the address immediately or need
      // to ensure the transaction is mined before proceeding.

      // await myToken.waitForDeployment(); // Optional: wait for deployment tx to be mined

      const contractAddress = await myToken.getAddress();
      console.log(`MyToken deployed to: ${contractAddress}`);
      console.log(`Transaction hash: ${myToken.deploymentTransaction().hash}`);

    }

    // We recommend this pattern to be able to use async/await everywhere
    // and properly handle errors.
    main().catch((error) => {
      console.error(error);
      process.exitCode = 1;
    }); 
    ```






2.  Create 2 files **'.env' and '.env.example'** in the project root to store credentials::
    ```bash
    .env
    .example.env
    ```
    Edit the **`.env`** file and add your private key and the 0G Testnet V3 RPC URL:
    ```dotenv
    PRIVATE_KEY="YOUR_PRIVATE_KEY_HERE_WITHOUT_0x"
    RPC_URL="https://evmrpc-testnet.0g.ai"   

    ```
    **Important:**
    *   Ensure the private key corresponds to an account with funds on the 0G Testnet V3.
    *   The private key should **not** have the `0x` prefix.
  
3.  Instal **dotenv**:
    ```
    npm install dotenv
    ```

  
## Compilation

Compile the contracts:

```bash
npx hardhat compile
```

## Deployment

Deploy the `MyToken` contract to the 0G Testnet V3:

```bash
npx hardhat run scripts/deploy.js --network og_testnet_v3
```

Hardhat will output the transaction hash and the address of the deployed contract.

## Verify Contract

- **Go to https://chainscan-galileo.0g.ai/contract-verification.**

- Upload the flattened.sol file (if there are imports like OpenZeppelin):
    ```bash
    npx hardhat flatten contracts/MyToken.sol > flattened.sol
    ```
- Enter:
    New contract address.
    Solidity version (e.g. 0.8.24).
    Contract Name (MyToken)
    License (MIT)
    Optimization settings (no).
    EVM Version to target (Paris)

    Submit for verification.
    **Congratulation your Contract Verified**









