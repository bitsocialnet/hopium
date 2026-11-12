# Hopium

**Hopium is an idea for a social trading app built on Bitsocial, where no company can hide a trader's calls or edit their track record.** Traders post calls from their own profile and link their wallets. Every reader's client checks each call against the trader's on-chain trades. Every token also gets a holder room, a peer-to-peer thread that only its holders can post in and moderate. Trades go through the reader's own wallet.

> **Status: idea stage.** This repository collects the design. Nothing here is built. It fits [Phase 5 of the Bitsocial master plan](https://bitsocial.net/#master-plan-phase-5) and depends on earlier phases: profile nodes, the follows of the [Phase 3 Twitter/X alternative](https://bitsocial.net/#master-plan-phase-3), public RPCs, `.bso` names and, for tips, Bitsocial Chain.

## The Thesis

Social trading apps such as [Fomo](https://fomo.family) and Pump.fun's mobile app are feeds of traders, their calls and their trades, with a buy button next to each one. The trades are already public on-chain. Everything around them sits on the operator's servers:

- **Visibility.** The operator decides which traders and calls appear in feeds. On 10 November 2026 a trader [accused Fomo](https://x.com/downsin/status/2120049629895229775) of manually hiding another trader's callouts; a reply in the same thread said that trader had deleted his own account. Readers had no way to check which happened.
- **Track records.** PnL, win rates and leaderboards are computed by the operator from its own database.
- **Rewards.** Pump.fun pays its best callers at least $300k a day, [according to AirdropAlert](https://airdropalert.com/blogs/fomo-vs-pump-fun/). In October 2026 it tied those rewards to followers' profits, days before Bubblemaps alleged that wallets linked to one of its callers traded ahead of the calls ([cryptonews.net](https://cryptonews.net/news/altcoins/33630912/)).

On Hopium, a call is a signed post in the trader's own Bitsocial profile, and a track record is something every reader's client can recompute from public data:

| | Fomo | Hopium |
|---|---|---|
| Who can hide a trader or a call | Fomo | Nobody across the network. Each community and client moderates its own view |
| Who removed a call | Not verifiable by readers | Signed: a deletion by the trader and a removal by a moderator are different publications |
| Where the track record comes from | Fomo's database | The trader's linked wallets on-chain, recomputable by anyone |
| Wallet | An embedded wallet the app sets up | The reader's own wallet |
| Fee per trade | 0.50%, gas covered except on Ethereum ([October 2026](https://airdropalert.com/blogs/fomo-vs-pump-fun/)) | Set by each client; competing clients can charge less or nothing |
| Chains | Solana, Base, BNB Chain, Ethereum, Monad, Robinhood Chain | Solana first; see [open-questions.md](open-questions.md#chains) |
| Can a trader leave with their followers | No | Yes. Followers follow the trader's key, which works in every Bitsocial client |

## Receipts on Every Call

The feature centralized apps don't offer is checking each call against the caller's own trades. Because calls are signed and wallets are linked, the client can show next to every call:

- whether the trader held the token when they called it, since when and how much,
- whether they sold in the minutes or hours after calling it,
- the price at the call and since,
- whether someone other than the trader witnessed when the call was posted.

This only covers wallets the trader linked. A trader can still trade from a wallet they didn't link, so receipts are evidence, not proof of honesty. See [how-it-could-work.md](how-it-could-work.md#receipts).

## Holder Rooms

Every token gets a room where only its holders can post, reply, vote and moderate. A room has no owner and no server: it is a peer-to-peer topic named after the token, and every peer checks each message's author against on-chain balances before passing it on. A discovery site can stop listing a coin, but nobody can close its room.

Rooms work with any token on a supported chain, including tokens launched on pump.fun, so Hopium complements launchpads instead of competing with them. A coin's page shows its room next to the calls that name it.

Holder rooms extend [pubsub voting](https://bsips.bitsocial.net/bsip-18), which already lets holders rank communities with no server, from votes to messages. See [how-it-could-work.md](how-it-could-work.md#holder-rooms).

The idea comes from [Esteban Abaroa](https://github.com/estebanabaroa), Bitsocial's original founder. In 2025, when the project was still called Plebbit, he proposed making every token a thread that only its holders can post in and moderate, run peer-to-peer with no servers, found through crossposts and holder-voted lists, and compatible with existing pump.fun tokens. He also proposed launching a token with every post, which is an [open question](open-questions.md#launching-tokens) here.

## What It Is Not

- **Not an exchange or a custodian.** The reader's wallet signs every trade. The client builds swaps through existing aggregators.
- **Not a launchpad.** Tokens launch elsewhere; calls and rooms work with any token. See [open-questions.md](open-questions.md#launching-tokens).
- **Not a new token.** Hopium needs no token of its own. Tips can use Bitsocial Chain.
- **Not a separate social network.** Calls are ordinary Bitsocial posts in the trader's profile, so they also show in the Phase 3 client and other Bitsocial clients, and following a trader is the same follow.
- **Not one community per coin.** Every Bitsocial community needs an always-online node, and new tokens launch far faster than anyone could run a node for each. A coin gets a holder room instead, which needs no node. A coin's team can still run a community for it.

## Why Bitsocial, Not a Blockchain

Trades are already on-chain, and Hopium reads them there. Calls are social posts. Putting each call on a chain would charge a fee for every post and make every node store them, and it would add nothing a signature doesn't already give: a signed call can't be forged or altered, and the trader's profile keeps its own copy if a community removes it.

The chain is only used where it is needed: for trades, optionally for timestamping calls, and on Bitsocial Chain for `.bso` names and tips.

## Documents

- [how-it-could-work.md](how-it-could-work.md): traders, linked wallets, profiles, calls, timestamps, receipts, feeds, holder rooms, trading, moderation and what would need a spec
- [open-questions.md](open-questions.md): design decisions not yet made

## License

[GPL-3.0-or-later](LICENSE).

## Ecosystem Context

- Bitsocial protocol docs: https://bitsocial.net
- Bitsocial Improvement Proposals: https://bsips.bitsocial.net
- Profiles: [BSIP-14 Author Communities](https://bsips.bitsocial.net/bsip-14)
- Holder voting: [BSIP-18 Pubsub Voting](https://bsips.bitsocial.net/bsip-18) and `bitsocialnet/pubsub-voting`
- Bitsocial Chain (names, tips): `bitsocialnet/bitsocial-chain` and https://chain.bitsocial.net
- Sibling idea-stage repos: [WeTube](https://github.com/bitsocialnet/wetube) (YouTube alternative), [decentralized-github](https://github.com/bitsocialnet/decentralized-github) (GitHub alternative), [Infinipedia](https://github.com/bitsocialnet/infinipedia) (encyclopedia), [prediction-markets](https://github.com/bitsocialnet/prediction-markets) (prediction markets with Bitsocial discussion)
