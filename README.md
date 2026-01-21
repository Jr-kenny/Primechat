# Prime Chat
**Decentralized Messaging Application**
Prime Chat is a high-performance, secure messaging platform built on the **Extensible Message Transport Protocol (XMTP)**. It bridges the gap between the UX of Web2 messaging and the cryptographic security of Web3, featuring a custom on-chain identity registry on Base.

##  System Architecture
Prime Chat leverages a multi-layered stack to ensure security, identity, and a seamless user experience.
### 1. Secure Messaging Layer (XMTP + MLS)
Communication is handled by the **Messaging Layer Security (MLS)** protocol. This architecture provides:
* **End-to-End Encryption:** Messages are encrypted at the client level.
* **Forward Secrecy:** Past sessions remain secure even if current keys are compromised.
* **Post-Compromise Security:** The protocol heals itself if a member is compromised.

### 2. Identity Layer: PrimeChatNameRegistry
We do not rely solely on hex addresses. Prime Chat implements a custom **Self-Sovereign Identity** system deployed on **Base**.

* **On-Chain Registry:** Users mint unique, human-readable handles directly on L2.
* **Anti-Phishing:** Reserved namespaces (e.g., `admin`, `support`) are enforced at the contract level.
* **Gas Efficiency:** Optimized for high-volume registration with negligible gas costs.

### 3. Client & Storage
* **State Management:** Powered by TanStack Query for efficient caching and synchronization.
* **Persistence:** Local SQLite (`.db3`) handling for message storage, designed for containerized resilience.

## 🛠 Tech Stack
| Component | Technology |
| --- | --- |
| **Messaging** | XMTP Browser SDK v3 (MLS) |
| **Smart Contracts** | Solidity (Base L2) |
| **Frontend** | React (Vite), TypeScript |
| **Styling/UI** | Tailwind CSS, Shadcn UI, Framer Motion |
| **Web3** | RainbowKit, Wagmi, Viem |


## ⚡ Quick Start

### 1. Installation

```bash
npm install @xmtp/browser-sdk wagmi viem @rainbow-me/rainbowkit @tanstack/react-query

```

### 2. Client Initialization
Initialize the XMTP client with a signer. We enforce strict version tagging for telemetry and debugging.

```typescript
const client = await Client.create(signer, {
  env: 'production', 
  appVersion: 'PrimeChat/1.0.0'
});

```

### 3. Synchronization

Sync historical messages and conversation states (Allowed/Requests/Blocked) in a single pass.

```typescript
// Synchronize all historical messages and consent states
await client.conversations.syncAll(['allowed']); 

```


## Smart Contract Interface
The `PrimeChatNameRegistry` maps Ethereum addresses to unique alphanumeric handles with strict validation.

```solidity
// Register your unique Prime Chat identity
function registerName(string memory _name) public {
    require(bytes(_name).length >= 3, "Name too short");
    // ... additional validation logic ...
    
    addressToName[msg.sender] = nameLower;
    nameToAddress[nameLower] = msg.sender;
    
    emit NameRegistered(msg.sender, nameLower);
}

```

---

##  Deployment & Ops

If deploying to containerized environments (Docker, Codespaces), adhere to the following strict constraints:

1. **Persistence Volumes:** You **must** map the SQLite `.db3` files to a persistent volume. Failure to do so will result in data loss and may trigger the **10-installation limit per Inbox ID**.
2. **Key Propagation:** For a new deployment to pull historical messages, a pre-existing installation of the same wallet must be online to serve the encryption keys via the MLS network.
3. **Geoblocking:** XMTP nodes enforce IP restrictions in sanctioned regions (e.g., Cuba, Iran, North Korea).


## 📄 License

**MIT License**

Copyright (c) 2026 Prime Chat

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

