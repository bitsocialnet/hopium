# How It Could Work

This is a sketch, not a spec. The conventions would become an Application-category BSIP in `bitsocialnet/BSIPs` once the open questions are settled.

## Layers

| Layer | What it is | Built from |
|---|---|---|
| Trader | An author key, optionally with a `.bso` name | Bitsocial authors ([BSIP-2](https://bsips.bitsocial.net/bsip-2)) and names ([BSIP-10](https://bsips.bitsocial.net/bsip-10), [BSIP-13](https://bsips.bitsocial.net/bsip-13)) |
| Linked wallets | Signed proofs that the trader controls wallet addresses | `author.wallets` ([BSIP-2](https://bsips.bitsocial.net/bsip-2)); proof formats not yet specified |
| Profile | The trader's own feed of calls, which only the trader can post to | An author community ([BSIP-14](https://bsips.bitsocial.net/bsip-14)) |
| Call | A post that names a token, with an optional thesis | A comment ([BSIP-2](https://bsips.bitsocial.net/bsip-2)) with a token reference |
| Group | A call group, a trading community or a coin's own community | An ordinary community ([BSIP-3](https://bsips.bitsocial.net/bsip-3)) |
| Trades | Swaps the trader made | On-chain, read from chain RPCs or indexers |
| Track record | PnL, call performance, receipts | Computed from calls and chain data by the client or an indexer |
| Trader list | A curated list of traders, such as a leaderboard | A community list ([BSIP-15](https://bsips.bitsocial.net/bsip-15)); profiles are communities |
| Trading | Buying and selling from the app | The reader's wallet and existing swap aggregators |
| Alerts | Notifications when a followed trader trades or calls | Replaceable alert services |

## Traders and Linked Wallets

A trader is a Bitsocial author: an Ed25519 key, optionally with a `.bso` name. Their identity is the key, so it works in every Bitsocial client.

[BSIP-2](https://bsips.bitsocial.net/bsip-2) already gives authors a `wallets` field, keyed by chain ticker, where each entry holds an address, a timestamp and a signature proving the author controls that address. The proof formats are not specified by a BSIP yet. Today `bitsocial-react-hooks` signs and validates only Ethereum wallets, with an EIP-191 signature over:

```json
{"domainSeparator":"pkc-author-wallet","authorAddress":"<author address>","timestamp":<unix seconds>}
```

Hopium needs:

- **A Solana proof.** An Ed25519 signature from the Solana wallet over the same message, with its own signature `type`.
- **A spec.** The proof formats in a BSIP, so every client verifies them the same way and computes the same track record.
- **A stable binding.** The author address is the `.bso` name when the author has one, otherwise the key. A proof signed over a name has to be redone when the name changes, and stops matching if the name is lost. A track record should survive a name change, so proofs for trading should bind to the key. See [open-questions.md](open-questions.md#wallet-linking).

A wallet proof shows the trader controls the address. It does not show they have no other wallets.

## Profiles

Each trader has a profile: an author community ([BSIP-14](https://bsips.bitsocial.net/bsip-14)) whose address is the trader's key, so anyone holding one of the trader's posts can find it.

- **Only the trader posts.** In the default feed-only configuration the profile has no pubsub topic, and its challenge rejects every publisher except the owner.
- **Calls outlive the groups they were posted in.** The profile holds the trader's own signed copy of each call. A group can remove a call from its board, but not from the trader's profile.
- **The trader's key never leaves their client.** The profile node holds a separate minter key. A trader can move to another node, including their own, without changing the profile's address, so their followers keep following the same key.
- **Browser and mobile users need hosted profile nodes.** They can't run an always-online node themselves, so Hopium can only ship once public RPCs host profiles. Desktop users can run their own.

Discussion of a call happens in the group where it was posted, or in the profile if the trader turns on the reply-able configuration.

## Calls

A call is an ordinary post that names a token. At minimum it needs the chain and the token's address, in a format every client parses the same way, for example a [CAIP-19](https://chainagnostic.org/CAIPs/caip-19) asset ID. Optional parts:

- direction: buy or sell,
- a thesis, as the post's text,
- a target or an exit condition,
- a disclosure that the call is paid.

A trader posts a call to their profile, to one or more groups, or both. When posting to a group, the client also crossposts it into the profile ([BSIP-12](https://bsips.bitsocial.net/bsip-12)), as BSIP-14 describes for mirroring an owner's activity.

**Edits.** An author can edit or delete their own comments ([BSIP-7](https://bsips.bitsocial.net/bsip-7)). The token reference must not change after posting, and clients should show that a call was edited.

**Deletions and removals.** A deletion is signed by the trader ([BSIP-7](https://bsips.bitsocial.net/bsip-7)); a removal is signed by a group's moderator ([BSIP-8](https://bsips.bitsocial.net/bsip-8)). Clients can always show which one happened and who did it. A trader who deletes losing calls would make their record look better, so indexers and archives that saw a call should keep it, and track records should count deleted calls. Comments can also name the author's previous comment (`author.previousCommentCid`), a per-author chain the author maintains, which may let clients notice gaps. How much of this to enforce is an [open question](open-questions.md#deleted-calls).

## Call Timestamps

A comment's `timestamp` is set by its author. A node rejects publications timestamped more than five minutes from its own clock ([BSIP-4](https://bsips.bitsocial.net/bsip-4)), but on a profile the trader controls the node that applies that rule. So a profile alone does not prove when a call was made: a trader could backdate a call to before a pump.

The client shows a call's time as witnessed only when someone other than the trader vouches for it:

| Witness | What it shows | Cost and trust |
|---|---|---|
| A group run by someone else | That group's node accepted the call within five minutes of its clock | Free; trusts that group's operator |
| Indexers | When each indexer first saw the call | Free; several independent indexers reduce trust in any one |
| An on-chain anchor | A hash of the call existed no later than a block's time | One transaction fee per call, or per batch of calls |

Without a witness, the client labels the time as stated by the trader.

## Receipts

For each call, the client or an indexer reads the trader's linked wallets and shows:

- the trader's position in the token at the time of the call: first buy, size, average price,
- sells after the call, for example within the first hour and the first day,
- the price at the call, at its peak since and now.

A trader's track record aggregates their calls and trades: realized and unrealized PnL from linked wallets, call returns, and how often the trader bought shortly before calling and sold shortly after.

Indexers compute this for many traders at once. Anyone can recompute it for a single trader from public chain data and their signed calls, so an indexer that reports wrong numbers can be caught.

Receipts can't see:

- wallets the trader didn't link,
- trades on centralized exchanges,
- wash trading between the trader's own wallets, unless both are linked,
- a reliable price for illiquid tokens.

## Following and Feeds

Following a trader means following their key, as in the Phase 3 client. The feed combines two sources with different speeds:

- **Trades** come straight from the chain. The client, or an alert service, watches followed traders' linked wallets and shows a trade seconds after it lands.
- **Calls** come from profiles. A feed-only profile has no pubsub topic, so readers see a new call when the profile's record is republished and fetched. How long that takes needs measuring, and memecoin calls lose value within minutes. Measuring it is the first experiment; see [open-questions.md](open-questions.md#latency).

Leaderboards and curated trader lists are community lists ([BSIP-15](https://bsips.bitsocial.net/bsip-15)) of profiles, published by whoever curates them. Clients choose which lists to show, and several can compete.

## Coin Pages

A coin page is assembled, not hosted. It shows:

- calls that name the token, from followed traders, followed groups and indexers,
- trades in the token by followed traders,
- holders and price from chain data and market data providers,
- a link to the coin's own community, if its team runs one.

## Trading

The client builds a swap through an existing aggregator for the chain, and the reader's wallet signs it. Two wallet options, not exclusive:

- **An external wallet** the reader already uses.
- **An in-app wallet** the client creates and the reader holds. `bitsocial-react-hooks` already gives every account an Ethereum wallet by using the account's private key as an Ethereum key (`getEthWalletFromPkcPrivateKey`). Doing the same on Solana would make the trading wallet the author key itself, since both use Ed25519, so a separate key derived from the account would be safer.

**Fees.** Each client may add a fee to the swaps it builds, as aggregators allow. Clients compete on fees, and a reader can switch clients without losing follows or traders.

**Caller rewards.** A client could send part of its fee to the caller whose call the reader bought from, inside the same swap transaction, so it's visible on-chain. That pays callers without a central rewards program, but has the same incentive problems as Pump.fun's callout rewards.

**Copy trading.** Automatic copy trading needs delegated signing. It is out of scope for a first version.

## Moderation

- **Network-wide, nobody removes a call.** That includes scams and illegal content.
- **Groups** moderate their own boards.
- **Clients** decide what their feeds show: hiding traders, warning on known rugs, honeypot checks and impersonation.
- **Impersonation.** Clients identify a trader by key or by a verified name, never by a profile's title or avatar ([BSIP-14](https://bsips.bitsocial.net/bsip-14#declaring-a-profile)).
- **Public RPC and gateway operators** can refuse to serve content, as with any Bitsocial client.

## Bitsocial Chain

Calls and profiles don't need a chain. Bitsocial Chain would carry:

| Use | Why it needs a chain |
|---|---|
| `.bso` names for traders and groups | A name must point to one key for everyone |
| Tips to callers | Payments need a shared ledger |
| Holder-gated voting on trader lists ([BSIP-18](https://bsips.bitsocial.net/bsip-18)) | Token holdings are harder to fake than accounts |
| Anchoring call hashes (optional) | An alternative to anchoring on the trading chain |

## What Would Need a Spec

- Wallet proof formats for Solana and EVM chains, and what they bind to.
- The call convention: the token reference, direction and paid-call disclosure.
- The timestamp witness and anchoring format.
- How track records are computed, so numbers match across clients and indexers. This may be a reference implementation rather than a spec.

The call and receipt conventions would go into one Application-category BSIP. Wallet proofs concern every Bitsocial client, so they may belong in BSIP-2 or a BSIP of their own.
