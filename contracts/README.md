# Pulse Contracts

This directory holds **Pulse token logic** and related smart contracts (when applicable).

## Contents

- `PulseToken.sol` — ERC-20 or equivalent token contract
- `staking/` — Staking logic for Premium access

## Token Utility

Premium features are unlocked by **staking Pulse tokens**. The app:

- Verifies wallet ownership
- Uses **read-only** chain interaction
- Does **not** support in-app token trading (for store compliance)

See [Token Utility](../docs/token-utility.md) for product-level details.

---

📖 See also: [Token Utility](../docs/token-utility.md) | [Roadmap](../docs/roadmap.md)
