# The Autonomous Web3 Bounty Hunter Blueprint
**Niche:** Web3 Freelance Automation & Decentralized Task Markets
**Income Stream:** Automated USDC Bounty Collection

## The New Business Model
Instead of selling developer tools or hunting for gigs manually on Upwork, you can launch an entirely autonomous freelance agency. 

Using the newly open-sourced **OpenHands** AI software engineer and the decentralized **TaskMarket (ERC-8194/ERC-8195)** smart contract protocol, you can deploy agents that:
1. Automatically scan the blockchain for open coding/auditing bounties.
2. Write the code, run tests, and generate the deliverable.
3. Submit the completed code back to the blockchain via IPFS.
4. Automatically claim USDC rewards directly into your hot wallet.

## The Architecture

### 1. The Brain: OpenHands
OpenHands serves as your AI worker. It lives in a Docker container, has full shell access, and can read/write code in your workspace.

### 2. The Bridge: TaskMarket MCP Server
OpenHands cannot naturally interact with smart contracts. We bridge this gap using the **Model Context Protocol (MCP)**. 

The `taskmarket_mcp_server.py` connects locally to your OpenHands instance and provides it with three tools:
- `get_open_bounties()`: Reads the TaskMarket smart contract for available gigs.
- `submit_work(task_id, ipfs_hash)`: Submits the finished code via the `TaskMarketForwarder` relay.
- `claim_reward()`: Withdraws earned USDC to your treasury.

### 3. The Orchestrator: MCP Server Toolkit
To prevent OpenHands from hallucinating smart contract addresses or entering infinite retry loops when the RPC node drops, the entire system is routed through the **MCP Server Toolkit**. This ensures the agent gracefully handles JSON-RPC errors and timeouts.

## Quick-Start Setup

1. **Deploy the TaskMarket Contract**
Ensure your local `TaskMarketForwarder.sol` and `TaskMarket.sol` are deployed to Base or Arbitrum.
```bash
cd taskmarket-contracts
forge script script/DeployForwarder.s.sol --rpc-url $BASE_RPC --broadcast
```

2. **Spin up the MCP Router**
Hook your new `taskmarket_mcp_server.py` into your universal router to enforce strict input schemas.
```python
from mcp_router import MCPRouter
router = MCPRouter()
router.register_server("taskmarket", "python taskmarket_mcp_server.py")
router.generate_openhands_config()
```

3. **Deploy the Agent**
Launch OpenHands with the generated MCP config. Give it the prompt: 
> "Check the TaskMarket for open bounties. If you find a React or Solidity task under 500 lines of code, write the implementation, save it locally, and submit the IPFS hash to the contract."

## Scaling the Agency
Once the loop is running, the agent will continuously farm bounties 24/7. 
To ensure your custom MCP servers (like your IPFS uploader and RPC nodes) don't crash the agent, validate them with **[MCP Linter Pro](https://shadowcraft41.gumroad.com/l/chrlxf)** before deploying.

*(Use code **LAUNCH50** for 50% off)*
