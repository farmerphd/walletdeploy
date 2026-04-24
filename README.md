# WalletDeploy

**The universal administrative signing layer for blockchain.**

Any hardware cold wallet. Any on-chain administrative action. Any blockchain. Directly from a browser — no CLI, no middleware, no hot keys.

**Live:** [walletdeploy.com](https://walletdeploy.com) · **GitHub:** [github.com/farmerphd/walletdeploy](https://github.com/farmerphd/walletdeploy) · **Patents Pending:** US 64/034,378 · US 64/041,067 · US 64/047,615

---

## The Problem

Every blockchain has the same architectural pattern:

1. Smart contracts and programs have an **owner**, **authority**, or **admin**
2. Security best practice: that authority should be a **hardware cold wallet**
3. Hardware cold wallets can't sign administrative transactions from existing tools:
   - CLI requires a keypair file — hardware wallets can't export private keys
   - Multisig tools (Squads, Gnosis Safe) route through their own program — blockchain runtimes reject administrative instructions via CPI/delegatecall
4. Result: developers use hot wallets (insecure) or complex middleware (broken)

**WalletDeploy solves this permanently.** Every blockchain runtime enforces the same security invariant: administrative instructions must be top-level transactions signed directly by the authority. WalletDeploy is the only tool that bridges hardware wallet cold storage to this requirement, on every chain.

---

## What's Live (Solana Mainnet)

| Feature | Status |
|---------|--------|
| Upgrade Program | ✅ Live |
| Deploy Program (CLI writes buffer) | ✅ Live |
| Deploy Program (No CLI — Patent #17) | ✅ Live |
| Transfer Upgrade Authority | ✅ Live |
| Extend Program | ✅ Live |
| Set Buffer Authority | ✅ Live |
| Recover SOL from Buffer | ✅ Live |
| Close Program | ✅ Live |
| Buffer Inspector (scan any wallet) | ✅ Live |
| Program Inspector (scan any wallet) | ✅ Live |
| Emergency Shutdown (sSLA agents) | ✅ Live |
| On-chain memo audit trail | ✅ Live |
| Mobile verify QR | ✅ Live |

**Proven on mainnet:** 50+ SOL recovered from locked buffers. Upgraded live programs. First-ever browser-based Solana program deployment with hardware cold wallet (Apr 22, 2026). All signed via Tangem NFC + WalletConnect — no CLI, no hot keys.

---

## Patent — No-CLI Program Deployment

US Provisional 64/041,067 (filed Apr 16, 2026)

**Before WalletDeploy:**
1. `solana program write-buffer ./program.so` (CLI — hot keypair required, cold wallet can't sign)
2. No path to deploy or upgrade with a hardware cold wallet — use a hot key or don't deploy

**After WalletDeploy (deploy.html):**
1. Drag `.so` file to browser
2. Tap cold wallet once — funds ephemeral keypair, creates buffer, records on-chain BWD delegation memo
3. Browser writes all chunks automatically — no wallet interaction
4. Tap cold wallet once — deploy or upgrade

Two cold wallet taps. Any program size. Any browser. No terminal.

### Delegated Ephemeral Keypair

The core innovation: a deterministic ephemeral keypair derived from `SHA256(programHash || coldWallet || sessionTimestamp)` handles all buffer write transactions autonomously. The cold wallet signs only twice — authorization and deployment. The ephemeral keypair is destroyed after use.

### BufferWriteDelegate (BWD) On-Chain Audit Trail

Every deployment records an immutable, cold-wallet-signed memo on Solana:

```json
{
  "type": "BWD",
  "authority": "ColdWalletPubkey",
  "delegate": "EphemeralPubkey",
  "buffer": "BufferPubkey",
  "expires": 1776368879,
  "maxBytes": 184904
}
```

This creates a compliance-grade audit trail that no CLI tool can match. Addresses GENIUS Act (stablecoin governance) and CLARITY Act (upgrade authority decentralization) requirements.

---

## Competitive Moat

| Tool | Cold wallet signing | Top-level instructions | Multi-chain | On-chain audit trail |
|------|--------------------|-----------------------|-------------|---------------------|
| Solana CLI | ❌ | ✅ | ❌ | ❌ |
| Squads | ✅ connect only | ❌ permanent limit | ❌ | ❌ |
| Gnosis Safe | ✅ connect only | ❌ delegatecall limit | ✅ EVM only | ❌ |
| **WalletDeploy** | **✅ any WalletConnect** | **✅** | **✅ all chains** | **✅** |

---

## Architecture

```
Hardware Cold Wallet (Tangem, Ledger, Keystone)
        ↓ WalletConnect
    Browser (deploy.html / app.html)
        ↓ Solana web3.js
    Solana Mainnet RPC
        ↓
    BPFLoader / Program / Buffer Accounts
```

- **Frontend:** Static HTML/CSS/JS — no framework, no build system
- **RPC:** Helius API behind `/rpc` nginx proxy (API key never exposed)
- **Hosting:** AWS EC2 (t3.micro), nginx, Let's Encrypt SSL
- **Keypair derivation:** TweetNaCl (universal browser support — Chrome, Firefox, Safari, Edge)

---

## Coming Next

EVM support (UUPS, TransparentProxy, ERC-20/721), Anchor IDL builder, CLI tool, VS Code extension.

---

## Security

- Cold wallet is the security anchor for all deploy/upgrade operations
- Ephemeral keypair has buffer write authority only — cannot deploy, upgrade, or transfer authority
- Ephemeral keypair holds only fee SOL (~0.001 SOL) — no user funds at risk
- On-chain BWD memo provides immutable audit record of every delegation
- Crash recovery: deterministic keypair re-derivation enables buffer SOL recovery if session is lost
- **Zero AI. Anywhere.** No AI-suggested transactions, no auto-fill, no inference layer, no ML models, no backend logic. Every instruction is deterministically constructed from your inputs, byte-for-byte verifiable before signing. What you see is exactly what gets signed — nothing more, nothing less.


---

## Files

| File | Purpose |
|------|---------|
| `app.html` | Main tool — all Solana program management features |
| `deploy.html` | No-CLI deploy/upgrade — Patent #17 |
| `walletdeploy_index.html` | Landing page (served as index.html) |
| `walletdeploy_verify.html` | Mobile transaction verification |

---

## License

MIT — see [LICENSE](LICENSE)

---

## Contact

- **Site:** [walletdeploy.com](https://walletdeploy.com)
- **GitHub:** [github.com/farmerphd/walletdeploy](https://github.com/farmerphd/walletdeploy)
- **Email:** dev@walletdeploy.com
- **Built by:** [Steve Farmer](https://x.com/Compustable), [CompuStable Inc.](https://compustable.com)

---

*WalletDeploy — the universal administrative signing layer for blockchain.*  
*First use: recovered 1.659 SOL from a locked Solana buffer using Tangem WalletConnect.*
