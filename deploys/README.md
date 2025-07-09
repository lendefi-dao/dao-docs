## Lendefi DAO Contract Addresses
# Lendefi DAO Contract Addresses
For interaction with these contracts, you should primarily use the proxy addresses, as these are the actual contract instances that users and other contracts will interact with.

The implementation addresses are the underlying logic contracts that can be upgraded in the future without changing the proxy addresses, maintaining the same contract identity and state.

[Ethereum Sepolia](https://sepolia.etherscan.io), [Ethereum Mainnet](https://etherscan.io), [Base Mainnet](https://basescan.org), [Binance Mainnet](https://bscscan.com), [Avalanche C-Chain Mainnet](https://snowtrace.io), [Arbitrum One Mainnet](https://arbiscan.io), [Polygon Mainnet](https://polygonscan.com)

| Contract | Implementation Address | Proxy Address |
|----------|------------------------|---------------|
| **TimelockController** | `0xa0539ecfe6f8f4d4cd9b2d000999a0fea6f128b2` | `0xab20ebc45b30a88a807e7230b4dfe899de3dd572` |
| **GovernanceToken** | `0x0d2c2c72ebe2a2543c027e6b25729dbd6f0a35b6` | `0x5e53aebe377efc92213514ec07f8ef3af426dd1d` |
| **Ecosystem** | `0xb1d00fef2ef08d835d543c3668ca1152b621c648` | `0x3ed13054a8e5b54ce898b6d5f647f9370358d140` |
| **LendefiGovernor** | `0x07c63da3b21783c878a60d30361eb47a6a846d45` | `0xb094c6ed74a83405a700d235496557bafdef2551` |
| **Treasury** | `0x3db09bc076995ff7aabea7f22a9ed79f28f81d1d` | `0x506ec8413f1fe3224e5c2b07bc888baefb098e5f` |
| **TeamManager** | `0x5cac32ca8c950a495e5ca5d47f619d89d72c48e4` | `0x74bea558c73b8a00b884a651ef0c7de0e44fe2f8` |
| **InvestmentManager** | `0x3179589e727f5e33eb697231e684c4fd8b07058d` | `0x58d6221a8e8e5a2d46467ba7a31bde5479fd821a` |
