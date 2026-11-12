# Open Questions

Design decisions not yet made. These need answers before Hopium moves from idea to spec.

## Who Calls First

The hardest question is not technical. Traders already have followings on Fomo, Pump.fun and X, and Pump.fun pays its best callers.

- Why would a trader with a following move? Candidates: they can't be hidden, their record is portable and checkable, and fees are lower.
- Which traders have been hidden, banned or denied rewards elsewhere, and would they come?
- Do receipts attract honest traders, or mostly scare off the callers readers currently follow?
- Can the app be useful with no traders at all, for example by showing receipts for any wallet a reader pastes?

## Wallet Linking

- What is the Solana proof format, and should EVM chains keep the current EIP-191 message?
- Should trading proofs bind to the author key instead of the author address, so a name change doesn't invalidate them?
- How many wallets can a trader link, and how does the client show a trader with few or new wallets?
- When a trader unlinks a wallet, do its past trades still count toward their record?
- Should clients flag unlinked wallets that indexers cluster with linked ones? Clustering is a heuristic, not proof.

## Call Format

- Which token reference format: CAIP-19, or a simpler chain-and-address pair?
- Which fields are required, and which are free text in the post?
- Is a reply that names a token also a call?
- How are paid calls disclosed, and can a client tell when a disclosure is missing?

## Call Timestamps

- Which witness do clients use by default: groups, indexers or on-chain anchors?
- If anchoring, on which chain, how often, and who pays for it?
- Should a call without a witness count toward a track record at all?

## Deleted Calls

- Should track records count deleted calls? If yes, the trader can't hide losing calls; if no, deletion rewrites the record.
- How long should indexers and archives keep calls the trader deleted?
- How should the client show a call it knows existed but can no longer fetch?

## Latency

- How long does a new call take to reach a follower through a hosted profile node?
- What is acceptable for memecoin calls: seconds or minutes?
- If profiles are too slow, should alert services watch profiles and push calls, and how is that kept replaceable?

## Trading

- Which swap aggregators per chain, and how does the client pick between them?
- In-app wallet, external wallet, or both? If in-app, derived from the Bitsocial account or kept separate, and how is it backed up?
- How do readers fund a wallet? Card and Apple Pay on-ramps are third-party services with their own identity checks.
- What fee, if any, should the first client charge?
- Should caller rewards exist, and how do they avoid paying for pumps?

## Chains

- Solana first, since most memecoin calls happen there?
- Which EVM chains next, and is cross-chain routing in scope? In October 2026 Pump.fun launched its own bridge and Fomo announced one.

## Legal and App Stores

- Does charging a fee on swaps, paying callers or showing trader performance create obligations in some jurisdictions?
- What do the App Store and Play Store require of a non-custodial trading client?
- Should the first release be web-only to avoid store review?

## Scams

- Which warnings should clients show by default: known rugs, honeypot checks, fresh or concentrated holders?
- How do clients handle traders impersonating each other with lookalike names and avatars?
- Should coin pages show calls from traders the reader doesn't follow, and from whom?

## Relationship to the Phase 3 Client

- Separate app, or a trading view inside the Phase 3 Twitter/X alternative? Profiles and follows are shared either way.
- Should calls render specially in other Bitsocial clients, for example as a token card with receipts?

## Indexers

- Who runs the indexers that compute track records, and what does indexing chain data cost?
- How do clients compare indexers and notice one that reports wrong numbers?
