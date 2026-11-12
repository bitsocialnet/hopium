# Roadmap

What is built, what is next, what each step waits on, and where each decision is written down. Update this file in the same change that finishes or reshapes an item.

Durations are rough estimates of focused work, not dates. Last reviewed 2026-11-12.

## Built

- [x] Idea docs: [README.md](README.md), [how-it-could-work.md](how-it-could-work.md), [open-questions.md](open-questions.md) (2026-11-12).

No code yet.

## What Blocks What

Hopium has four parts with different dependencies. Three can start now.

| Part | Depends on | State of the dependency (2026-11-12) |
|---|---|---|
| Receipts and track records | Solana chain data | Available from third-party providers. Nothing in Bitsocial blocks it |
| Reading calls | Profiles ([BSIP-14](https://bsips.bitsocial.net/bsip-14)), crossposts ([BSIP-12](https://bsips.bitsocial.net/bsip-12)) | Implemented in pkc-js. `bitsocial-react-hooks` has no profile or Solana wallet support yet |
| Trading | A Solana wallet and a swap aggregator | Available. Nothing in Bitsocial blocks it |
| Posting calls from a browser or phone | A hosted profile node over the RPC protocol ([BSIP-17](https://bsips.bitsocial.net/bsip-17), [BSIP-11](https://bsips.bitsocial.net/bsip-11)) | Not available. No public RPC hosts profiles yet. Desktop users with their own node are not blocked |
| Holder rooms | Pubsub voting ([BSIP-18](https://bsips.bitsocial.net/bsip-18)) extended to messages, Solana and fungible balances | Not available. See [milestone 6](#6-holder-rooms) |
| Names, tips | `.bso` names ([BSIP-13](https://bsips.bitsocial.net/bsip-13)), Bitsocial Chain | Optional. Traders work as bare keys until then |

So the client can show receipts, calls from demo traders and a buy button before any trader can sign up from a phone. Open sign-up waits on hosted profiles; holder rooms wait on protocol work.

## Next

### 1. Receipts for any wallet

A web client that needs no Bitsocial data: paste a Solana wallet or token, see positions, buys and sells over time, and PnL. It answers "can the app be useful with no traders at all" from [open-questions.md](open-questions.md#who-calls-first) and builds the code every later milestone reuses.

- [ ] Client scaffold on the stack of 5chan and WeTube (React, Vite, `bitsocial-react-hooks`, static, hash routing) with the shared agent workflow.
- [ ] Choose the chain data source. A plain RPC cannot serve a wallet's trade history with prices quickly; an indexing provider is needed, behind an interface so it can be replaced. Decision needed: which provider, and who holds the API key for a static client (a small proxy, or the reader's own key in settings).
- [ ] Receipt computation as a pure library: given a wallet's trades and a time, return the position, first buy, average price and sells after. Tested with recorded transactions, so numbers can be compared across clients later.
- [ ] Wallet and coin pages.

Estimate: about 1 week. Blocked by: nothing.

### 2. Calls, read-only

Calls from demo trader profiles, shown with receipts.

- [ ] **Latency experiment first**, as [open-questions.md](open-questions.md#latency) says: run a feed-only profile, publish calls, and measure how long a browser reader takes to see each one. The result decides whether an alert service is part of the first version.
- [ ] Call format v0 for the prototype: how a post names a token ([open-questions.md](open-questions.md#call-format)). Recorded here as a prototype convention until the BSIP.
- [ ] Solana wallet proof v0: an Ed25519 signature from the wallet, bound to the author key. Signing and verification go into `bitsocial-react-hooks`, next to the existing Ethereum proof, not into this client.
- [ ] Demo trader profiles on a Bitsocial node we run, each with a linked wallet and real calls, the way WeTube's demo channel works.
- [ ] Feed of followed traders, trader page with track record, coin page listing calls that name the token. Following is a subscription to the profile, kept in the account.
- [ ] Each call shows its receipts and whether its time is witnessed or only stated by the trader.

Estimate: 1 to 2 weeks after milestone 1. Blocked by: nothing; needs a `bitsocial-react-hooks` release for the Solana proof.

### 3. Trading

- [ ] Connect an external Solana wallet.
- [ ] Buy and sell from a call or a coin page through a swap aggregator, signed by the reader's wallet.
- [ ] Default warnings before a buy ([open-questions.md](open-questions.md#scams)).
- [ ] No client fee and no caller rewards in the first version; both stay [open](open-questions.md#trading).

Estimate: about 1 week. Blocked by: nothing technical. The [legal questions](open-questions.md#legal-and-app-stores) need an answer before a public deployment with a buy button; local testing does not.

### 4. Posting calls

The step that makes Hopium usable by traders other than the demo ones.

- [ ] Sign in with a Bitsocial profile and post a call to it, and to a group with a crosspost into the profile.
- [ ] Link a wallet from the app.
- [ ] Edit and delete, with the client showing which happened.
- [ ] Desktop path: a trader running their own node can do all of the above as soon as the client supports it.
- [ ] Browser and mobile path: the profile is hosted through a public RPC, with the trader's key staying in the client.

Estimate: about 1 week of client work for the desktop path. The browser and mobile path is blocked by hosted profiles on a public RPC, which no service offers yet. WeTube's sign-in waits on the same thing, so the work is shared.

### 5. Indexer

- [ ] An indexer that follows the profiles in trader lists: track records for many traders at once, first-seen times as timestamp witnesses, leaderboards published as community lists ([BSIP-15](https://bsips.bitsocial.net/bsip-15)), deleted calls kept ([open-questions.md](open-questions.md#deleted-calls)).
- [ ] Alerts for followed traders' trades and calls, as a replaceable service.
- [ ] Provider settings in the client, so indexers can compete and be compared.

Estimate: 2 weeks. Blocked by: nothing, but it has little to index before milestone 4 opens sign-up.

### 6. Holder rooms

The furthest part. Pubsub voting as specified in BSIP-18 and shipped in `@bitsocial/pubsub-voting` covers one case: one vote per wallet, gated by a soulbound NFT on an EVM chain. A holder room needs four things it does not have:

- [ ] **Messages.** Bundles carry votes. Posts and replies need a message format, size limits, history and its own spam analysis.
- [ ] **A fungible-balance gate.** `erc20-balance` is in the code but unregistered: one balance moved through several wallets backs several votes. The planned fix reads the balance at two blocks an expiry window apart, which needs archive RPC access.
- [ ] **Solana.** Bundles are EIP-712 signatures and freshness is counted in EVM blocks. Solana needs Ed25519 signatures, slot-based buckets and an SPL balance rule. BSIP-18 lists a chain-independent criteria document as a future major revision; Solana support belongs in that revision.
- [ ] **Moderation votes** tallied the same by every client.

An EVM-only room on an ERC-20 token needs the first two and could come first as an experiment. Estimate: 4 to 8 weeks of protocol work in `pubsub-voting` before any client work, and the design questions in [open-questions.md](open-questions.md#holder-rooms) come first. Blocked by: the items above.

## BSIPs

All existing BSIPs are drafts. Hopium builds on BSIP-2, 7, 8, 10, 11, 12, 14, 15, 16, 17 and 18, and would add:

| BSIP | Category | Requires | Written when |
|---|---|---|---|
| Author wallet proofs: the proof formats inside `author.wallets` for EVM and Solana, and binding to the author key. BSIP-2 defines the field but leaves the formats unspecified | Core, or an amendment to BSIP-2 | 2, 10 | After milestone 2 proves the Solana format. Concerns every client, so it should not wait for the rest of Hopium |
| Calls: the token reference, direction, paid-call disclosure, timestamp witnesses, and which edits are allowed | Application | 2, 7, 12, 14, wallet proofs | After milestone 4, once real posting has tested the format |
| Holder rooms | Application | 18, or its next major revision | With milestone 6 |

Track record computation is a reference implementation (the library from milestone 1), not a BSIP, unless clients start disagreeing on numbers.

Changes to existing BSIPs that Hopium would ask for: wallet proof formats in or alongside BSIP-2, and Solana, fungible gates and messages in BSIP-18.

## Later

- [ ] In-app wallet derived from the account ([open-questions.md](open-questions.md#trading)).
- [ ] EVM chains.
- [ ] `.bso` names for traders and tips on Bitsocial Chain.
- [ ] On-chain anchoring of call hashes.
- [ ] Client fee and caller rewards, if they are adopted.
- [ ] Token launches for a post ([open-questions.md](open-questions.md#launching-tokens)).
- [ ] Mobile apps.
- [ ] Copy trading.
