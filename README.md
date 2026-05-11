# WalletDeploy

The universal administrative signing layer for blockchain.

Any hardware cold wallet. Any on-chain administrative action. Any blockchain. Directly from a browser — no CLI, no middleware, no hot keys.

**Live:** [walletdeploy.com](https://walletdeploy.com) · **GitHub:** [github.com/farmerphd/walletdeploy](https://github.com/farmerphd/walletdeploy) · **Patents Pending:** US 64/034,378 · US 64/041,067 · US 64/047,615 · US 64/054,668

**🏛️ Submitted to [Colosseum Frontier Hackathon](https://www.colosseum.org/)**

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
| Deploy Program (No CLI — Patent #17) | ✅ Live |
| Transfer Upgrade Authority | ✅ Live |
| Extend Program | ✅ Live |
| Set Buffer Authority | ✅ Live |
| Recover SOL from Buffer | ✅ Live |
| Close Program | ✅ Live |
| **Freeze Program** (irreversible — remove authority forever) | ✅ Live |
| Buffer Inspector (scan any wallet) | ✅ Live |
| Program Inspector (scan any wallet) | ✅ Live |
| Emergency Shutdown (sSLA agents) | ✅ Live |
| On-chain memo audit trail | ✅ Live |
| Mobile verify QR | ✅ Live |
| **VS Code Extension** | ✅ [Marketplace](https://marketplace.visualstudio.com/items?itemName=CompuStableInc.walletdeploy) |

**Proven on mainnet:** 50+ SOL recovered from locked buffers. Upgraded live programs. First-ever browser-based Solana program deployment with hardware cold wallet. All signed via Tangem NFC + WalletConnect — no CLI, no hot keys.

---

## Wallets Supported

| Wallet | Connection | Status |
|--------|-----------|--------|
| 👻 Phantom | Browser extension | ✅ Live |
| 🔥 Solflare | Browser extension | ✅ Live |
| 🟣 Tangem | WalletConnect (NFC) | ✅ Live |
| 🔷 Keystone | WalletConnect (QR) | ✅ Live |
| ⬛ Ngrave | WalletConnect (QR) | ✅ Live |
| 🔲 Ellipal | WalletConnect (Air-gapped) | ✅ Live |
| Any WalletConnect v2 wallet | QR code | ✅ Live |

Start with Phantom or Solflare (no hardware wallet needed). Graduate to cold storage when your program has real value on mainnet.

---

## Patent — No-CLI Program Deployment

**Before WalletDeploy:**
```
solana program write-buffer ./program.so  (CLI — hot keypair required)
# No path to deploy or upgrade with a hardware cold wallet
```

**After WalletDeploy (deploy.html):**
1. Drag .so file to browser
2. Tap cold wallet once — funds ephemeral keypair, creates buffer, records on-chain BWD delegation memo
3. Browser writes all chunks automatically — no wallet interaction
4. Tap cold wallet once — deploy or upgrade

**Two cold wallet taps. Any program size. Any browser. No terminal.**

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
  "maxBytes": 184904
}
```

This creates a compliance-grade audit trail that no CLI tool can match. Addresses GENIUS Act (stablecoin governance) and CLARITY Act (upgrade authority decentralization) requirements.

---

## VS Code Extension

Search **"WalletDeploy"** in the VS Code Marketplace or install directly: [CompuStableInc.walletdeploy](https://marketplace.visualstudio.com/items?itemName=CompuStableInc.walletdeploy)

**Features:**
- CodeLens on `declare_id!()` macros — inline Upgrade / Transfer Authority buttons
- Hover info on program IDs — shows authority, data size, upgradeable status
- No-CLI Deploy & Upgrade — same ephemeral keypair flow as deploy.html
- All admin operations: Transfer Authority, Recover SOL, Extend, Close, Freeze
- Scan for Programs You Control / Scan for Locked Buffers
- Phantom, Solflare (via local bridge), WalletConnect (via QR)
- On-chain memo audit trail on every operation

---

## Freeze Program

Permanently remove upgrade authority. Irreversible. The gold standard for decentralization.

- Calls `SetUpgradeAuthority` with `None` as the new authority
- UI requires typing the program ID twice to confirm
- Explicit warning: "This is permanent and irreversible"
- On success: Solana Explorer link + shareable on-chain proof that program is frozen
- **Compliance angle**: Frozen program is the strongest CLARITY Act decentralization argument

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
    or Phantom / Solflare (browser extension)
        ↓ WalletConnect / window.solana / window.solflare
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

## Devnet Testing

Test all features without real SOL:

- **[walletdeploy.com/app_devnet.html](https://walletdeploy.com/app_devnet.html)** — full devnet admin tool
- **[walletdeploy.com/deploy_devnet.html](https://walletdeploy.com/deploy_devnet.html)** — devnet deploy/upgrade

Works with Phantom browser extension (import a devnet keypair) or any WalletConnect wallet pointed at devnet.

---

## Coming Next

- CLI tool (`npm install -g walletdeploy-cli`)
- EVM support (UUPS, TransparentProxy, ERC-20/721)
- Anchor IDL instruction builder
- CI/CD human-in-the-loop signing

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
| `app_devnet.html` | Devnet version — Phantom extension support, orange banner |
| `deploy.html` | No-CLI deploy/upgrade |
| `deploy_devnet.html` | Devnet version of deploy.html |
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
- **Twitter:** [@Compustable](https://x.com/Compustable)

---

**WalletDeploy** — the universal administrative signing layer for blockchain.

*First use: recovered 1.659 SOL from a locked Solana buffer using Tangem WalletConnect.*
