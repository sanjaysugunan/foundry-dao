# Foundry DAO

A minimal on-chain DAO built with [Foundry](https://book.getfoundry.sh/), demonstrating token-based governance for Solidity smart contracts. Any transaction the DAO wants to execute must first be proposed and voted on by token holders.

## How it works

1. A treasury/target contract is controlled by the DAO — it cannot be called directly by any single address.
2. Every transaction the DAO wants to send (e.g. calling a function on that contract) must be created as a **proposal** and voted on by the community before it can execute.
3. Voting power is represented by an **ERC20 governance token** (one token, one vote). This is a simple starting model — see [Limitations](#limitations) below for why it isn't ideal for production use.

This mirrors the standard OpenZeppelin `Governor` + `ERC20Votes` + `TimelockController` pattern: token holders vote, approved proposals are queued in a timelock, and only the timelock (not any individual account) is authorized to execute calls against the governed contract.

## Project structure

```
.
├── src/                 # Solidity contracts (governance token, governor, timelock, governed contract)
├── test/                # Foundry tests
├── lib/                 # Dependencies (installed via git submodules / forge install)
├── .github/workflows/   # CI configuration
├── foundry.toml         # Foundry project configuration
└── foundry.lock         # Locked dependency versions
```

> Contract names may differ slightly from the description above — check `src/` for the exact files in this repo.

## Requirements

- [Foundry](https://book.getfoundry.sh/getting-started/installation) (`forge`, `cast`, `anvil`)
- [Git](https://git-scm.com/)

## Getting started

Clone the repo and install dependencies:

```bash
git clone https://github.com/sanjaysugunan/foundry-dao
cd foundry-dao
forge install
```

Build the contracts:

```bash
forge build
```

Run the test suite:

```bash
forge test
```

Run tests with more verbosity (useful for debugging):

```bash
forge test -vvvv
```

Check test coverage:

```bash
forge coverage
```

Format code:

```bash
forge fmt
```

## Typical governance flow

Once deployed, the DAO lifecycle generally looks like this:

1. **Propose** — A token holder with enough voting power submits a proposal describing the target contract, calldata, and value to send.
2. **Vote** — Token holders vote for/against/abstain during the voting period.
3. **Queue** — If the proposal succeeds, it's queued in the timelock.
4. **Execute** — After the timelock delay passes, anyone can execute the proposal, and the underlying transaction is finally sent from the DAO-controlled contract.

You can drive this flow locally using `forge script` deployment scripts and `cast` calls against a local Anvil chain, or write Foundry tests that simulate the full propose → vote → queue → execute cycle (see `test/`).

## Limitations

- **Plutocratic voting**: using a raw ERC20 for voting means voting power is proportional to token holdings, which can concentrate control in a few large holders. Consider quadratic voting, reputation-based (non-transferable) tokens, or delegation safeguards for a production system.
- This is a learning/reference project, not an audited production-ready DAO framework. Do not deploy it to mainnet with real funds without a proper security review.

## Contributing

Issues and pull requests are welcome — in particular around exploring alternative, less plutocratic voting models.

## License

No license file is currently included in this repository. Add one (e.g. MIT) if you intend for others to reuse this code.