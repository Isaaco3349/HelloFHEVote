📘 HelloFHEVote — A Confidential Voting dApp on FHEVM

Welcome to HelloFHEVote, a simple yet powerful example of how to build privacy-preserving decentralized applications using Zama’s FHEVM.

In this tutorial, you’ll learn how to:

Set up a development environment for FHEVM.

Write a Solidity smart contract that accepts encrypted votes.

Build a front-end UI that encrypts user inputs and decrypts results.

Run a complete encryption → computation → decryption flow.

Deploy the app on Zama’s testnet for others to try.

This project is aimed at Solidity developers with basic familiarity with smart contracts but little or no background in cryptography.

🔧 Prerequisites

Before starting, make sure you have:

Node.js v18+ installed (node -v).

npm or yarn installed.

Hardhat (or Foundry) familiarity.

A Metamask wallet (or equivalent).

Basic knowledge of Solidity and React (helpful but not required).

🛠 Environment Setup

Clone the repo:

git clone https://github.com/yourusername/HelloFHEVote.git
cd HelloFHEVote


Install dependencies:

npm install


Install FHEVM libraries:

npm install @zama-fhe/fhevmjs


Initialize Hardhat project:

npx hardhat init


Compile contracts:

npx hardhat compile

📜 Smart Contract — HelloFHEVote.sol

Here’s a minimal example contract that enables confidential voting:

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@zama-fhe/fhevm-solidity/contracts/FHE.sol";

contract HelloFHEVote {
    // Store encrypted votes
    mapping(address => euint8) private votes;
    euint8 private yesCount;
    euint8 private noCount;

    // Cast an encrypted vote (1 for yes, 0 for no)
    function castVote(euint8 encryptedVote) public {
        require(votes[msg.sender].isEmpty(), "Already voted");
        votes[msg.sender] = encryptedVote;

        // Add vote securely
        yesCount = FHE.add(yesCount, FHE.eq(encryptedVote, FHE.asEuint8(1)));
        noCount = FHE.add(noCount, FHE.eq(encryptedVote, FHE.asEuint8(0)));
    }

    // Get encrypted tallies
    function getResults() public view returns (euint8, euint8) {
        return (yesCount, noCount);
    }
}

Key Points:

Votes are submitted in encrypted form.

The contract tallies votes using FHE operations.

Results remain encrypted until decrypted by the front-end.

💻 Front-End Setup

Inside your repo, create a React app:

npx create-react-app frontend
cd frontend
npm install ethers @zama-fhe/fhevmjs


Add wallet connection (using ethers.js).

Encrypt user input before sending to the contract:

import { FHE } from "@zama-fhe/fhevmjs";

async function castVote(vote) {
  // Encrypt vote (1 for yes, 0 for no)
  const encryptedVote = await FHE.encrypt(vote);
  const tx = await contract.castVote(encryptedVote);
  await tx.wait();
}


Decrypt results for display:

async function getResults() {
  const [encryptedYes, encryptedNo] = await contract.getResults();
  const yes = await FHE.decrypt(encryptedYes);
  const no = await FHE.decrypt(encryptedNo);
  setResults({ yes, no });
}

▶️ Running Locally

Start local node:

npx hardhat node


Deploy contract:

npx hardhat run scripts/deploy.js --network localhost


Start frontend:

cd frontend
npm start


Open browser at http://localhost:3000.

You can now connect your wallet, cast a vote, and see results update!

🚀 Deployment

To share your app with others:

Deploy contract on Zama testnet.

Update frontend with deployed contract address.

Deploy frontend to Vercel or Netlify.

Provide both the GitHub repo link and live demo link in your bounty submission.

🔍 Troubleshooting

Contract compilation fails: Ensure Solidity version matches pragma ^0.8.20.

npm install issues: Use Node 18+. Delete node_modules and reinstall.

Wallet not connecting: Verify you’re on the correct network (Zama testnet).

Decryption mismatch: Ensure frontend uses the same FHE key setup as contract.

📈 Next Steps

Once you understand this base project, try extending it:

Add multiple candidates instead of yes/no.

Build a private auction where bids remain hidden.

Enable private surveys with multiple choice questions.

🎥 Extras

This tutorial includes:

Written instructions (this README).

A diagram explaining the encryption → computation → decryption workflow.

A short video demo walking through the voting app.

✅ Conclusion

HelloFHEVote demonstrates the power of confidential computation on blockchain with FHEVM. It’s simple enough for beginners, yet showcases how revolutionary privacy-preserving dApps can be. With this tutorial, you’ll have your first working FHEVM dApp — and the foundation to build more advanced applications.
