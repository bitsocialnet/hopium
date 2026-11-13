# Hopium

<img src="hopium.png" alt="Pepe the Frog breathing from a tank labeled HOPIUM" width="300">

**Hopium is an idea for a social trading app built on Bitsocial, where no company can hide a trader's calls or edit their track record.** Traders post calls from their own profile and link their wallets. Every reader's client checks each call against the trader's on-chain trades. Every token also gets a holder room, a peer-to-peer thread that only its holders can post in and moderate. Trades go through the reader's own wallet.

> **Status: set aside on 13 November 2026.** Nothing here was built and nothing is planned. This repository is archived as a record of the idea and of why it was not pursued, for anyone who thinks of building such an app later.

## Why It Was Set Aside

The design was shared with an experienced trader who has used Fomo and Pump.fun's app for a long time. Their feedback, and a second look at the pitch, left no reason strong enough to build it:

- **Censorship resistance matters little for calls.** A profile or a community on a web2 platform has to last for years, so its owner cares who can remove it. A call is relevant for minutes or hours. The known complaints of hidden calls are few, and traders do not choose an app over them.
- **Fomo won on its trading engine.** Its users came for trading across chains from one balance. Hopium would build swaps through existing aggregators and leave bridging to the reader, which is a worse experience, and a small team cannot out-build Fomo or Pump.fun on execution.
- **Fees are already too low to undercut.** Nobody changes apps to save a fraction of 0.5%.
- **Track records are mostly verifiable already.** A trader's profit and loss on these apps is computed from on-chain wallets, and anyone can recheck a wallet with a third-party tracker. Receipts per call (did the caller hold before the call and sell after it) may be new, but a tracker site could add them without Bitsocial, and a caller can always trade from a wallet they never linked.
- **Holder rooms solve a problem holders don't report.** Token chats on these apps are not being censored. Rooms are also the most expensive part of the design: they need pubsub voting extended to messages, fungible balances and Solana ([roadmap.md](roadmap.md#6-holder-rooms)).
- **Rewards steered by token holders become bribes.** Redirecting caller rewards by a holder vote, to stop calls made for exit liquidity, would let callers pay voters privately. It ends up as the system Pump.fun already runs, with less money.
- **It could reflect badly on Bitsocial.** An app whose scam calls nobody can remove network-wide, carrying the Bitsocial name, has a cost that no product advantage offsets.

What is left is a demonstration that such an app can be built on Bitsocial. [WeTube](https://github.com/bitsocialnet/wetube) makes that point with a clearer case.

### What Could Change This

- An open protocol for cross-chain swaps and limit orders that a non-custodial client can use, so trading no longer depends on owning an engine.
- Evidence that traders want receipts per call and that no existing app or tracker shows them.
- Community tokens on Bitsocial Chain. A community that issues its own token has a reason for a holder-only room that a memecoin chat does not.

## Where the Idea Came From

Holder rooms come from [Esteban Abaroa](https://github.com/estebanabaroa), Bitsocial's original founder. The idea has two stages.

**2022: a token for every community.** In the project's public Telegram group, when it was still called Plebbit:

- [18 June 2022](https://t.me/bitsocialnet/216263): "each subplebbit owner can make their own token and nft and integrate them into their own sub functionality, which means if you hit it big and build a successful subplebbit, you will be rich". He added that the tools "will take several years to be all ready".
- 10 July 2022: "in the future each subplebbit should have its own token".
- [11 November 2022](https://t.me/bitsocialnet/310230): votes that weigh more for holders of a token the community owner chooses, always optional.

**2024 and 2025: a room for every token.** In a community group chat and in the team's chat, which are not public:

- 30 October 2024: "once we have the token voting thing, we could create some kind of pump.fun and the token page would be some p2p chatroom where only token holders can talk". He said then that he had never used pump.fun.
- 7 November 2024: launching a coin on pump.fun would automatically give it a holder-only community that nobody runs. "it will be either quite revolutionary, or completely useless, we'll see". It would hold only "a few ephemeral topics/messages", with no history.
- 1 December 2024: an automatic community for every token in existence, "like a trollbox", where large holders are moderators and heavily downvoted posts are deleted.
- 18 January 2025, the full proposal, in the team's chat:
  - "whenever a plebbit post is published, a token and liquidity pool launches referencing the post".
  - The post belongs to no community. Its "votes and replies are currated by the token owners, using our pubsub token voting protocol (that isnt built yet)".
  - "the value of the post's token comes from the ability to reply to and vote on replies if you own the token".
  - The poster can link a livestream and change the link when the stream is taken down.
  - "it would also be compatible with existing pump.fun tokens, with any token on any blockchain, so we don't really have to be significantly better than pump.fun and beat them, we're complementary to pump.fun".
- 2 September 2025: a low-cap coin's holders chatting "without any intermediary, not even any sub owner" is "a novel concept that could develop a real use case".

He also raised the two doubts that weighed against it here. On 8 December 2024, arguing against a chat client: "there is basically no censorship problem for instant messaging communities". On 3 June 2025, after a developer found that a pump.fun clone needs a centralized API for buys and for its main page: "is it even worth it to build on plebbit if thats the case?"

Hopium kept the room and dropped the token launched with every post. The calls, track records and trading were added in November 2026 and are not his. The 2022 idea, a token for each community, continued separately in the plans for [Bitsocial Chain](https://github.com/bitsocialnet/bitsocial-chain).

## The Idea as Designed

The rest of this README and the other documents are the design as written before the decision, kept unchanged.

## The Thesis

Social trading apps such as [Fomo](https://fomo.family) and Pump.fun's mobile app are feeds of traders, their calls and their trades, with a buy button next to each one. The trades are already public on-chain. Everything around them sits on the operator's servers:

- **Visibility.** The operator decides which traders and calls appear in feeds. On 10 November 2026 a trader [accused Fomo](https://x.com/downsin/status/2120049629895229775) of manually hiding another trader's callouts; a reply in the same thread said that trader had deleted his own account. Readers had no way to check which happened.
- **Track records.** PnL, win rates and leaderboards are computed by the operator from its own database.
- **Rewards.** Pump.fun pays its best callers at least $300k a day, [according to AirdropAlert](https://airdropalert.com/blogs/fomo-vs-pump-fun/). In October 2026 it tied those rewards to followers' profits, days before Bubblemaps alleged that wallets linked to one of its callers traded ahead of the calls ([cryptonews.net](https://cryptonews.net/news/altcoins/33630912/)).

On Hopium, a call is a signed post in the trader's own Bitsocial profile, and a track record is something every reader's client can recompute from public data:

| | Fomo | Pump.fun app | Hopium |
|---|---|---|---|
| Who can hide a trader or a call | Fomo | Pump.fun | Nobody across the network. Each community and client moderates its own view |
| Who removed a call | Not verifiable by readers | Not verifiable by readers | Signed: a deletion by the trader and a removal by a moderator are different publications |
| Where the track record comes from | Fomo's database | Pump.fun's database | The trader's linked wallets on-chain, recomputable by anyone |
| Wallet | An embedded wallet the app sets up | A wallet the user connects, or an embedded one generated through Privy ([terms](https://pump.fun/docs/terms-and-conditions)) | The reader's own wallet |
| App fee per trade | 0.50%, gas covered except on Ethereum ([October 2026](https://airdropalert.com/blogs/fomo-vs-pump-fun/)) | 0.45%, or 0.40% with a referral code ([October 2026](https://www.tokenpost.com/news/business/33804)) | Set by each client; competing clients can charge less or nothing |
| Who pays callers | No rewards program for callers | Pump.fun, from its own pool, by its own ranking | No central program. A client could share its fee with the caller inside the swap; see [open-questions.md](open-questions.md#trading) |
| Chains | Solana, Base, BNB Chain, Ethereum, Monad, Robinhood Chain | Solana, plus Robinhood Chain, BNB Chain, Base, Arc, Ink and Ethereum through its bridge | Every chain Fomo supports from the start, then as many as possible; see [open-questions.md](open-questions.md#chains) |
| Can a trader leave with their followers | No | No | Yes. Followers follow the trader's key, which works in every Bitsocial client |

The app fee is what each app adds. The fee of the token's pool or launchpad, such as 1.25% on a Pump.fun bonding curve, is paid on top of it in every app, and would be on Hopium too.

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

The idea comes from [Esteban Abaroa](https://github.com/estebanabaroa), Bitsocial's original founder ([Where the Idea Came From](#where-the-idea-came-from)). In 2025, when the project was still called Plebbit, he proposed making every token a thread that only its holders can post in and moderate, run peer-to-peer with no servers, found through crossposts and holder-voted lists, and compatible with existing pump.fun tokens. He also proposed launching a token with every post, which is an [open question](open-questions.md#launching-tokens) here.

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
- [roadmap.md](roadmap.md): the milestones a build would have had, what blocks each one and which BSIPs Hopium would add

## License

[GPL-3.0-or-later](LICENSE).

## Ecosystem Context

- Bitsocial protocol docs: https://bitsocial.net
- Bitsocial Improvement Proposals: https://bsips.bitsocial.net
- Profiles: [BSIP-14 Author Communities](https://bsips.bitsocial.net/bsip-14)
- Holder voting: [BSIP-18 Pubsub Voting](https://bsips.bitsocial.net/bsip-18) and `bitsocialnet/pubsub-voting`
- Bitsocial Chain (names, tips): `bitsocialnet/bitsocial-chain` and https://chain.bitsocial.net
- Sibling idea-stage repos: [WeTube](https://github.com/bitsocialnet/wetube) (YouTube alternative), [decentralized-github](https://github.com/bitsocialnet/decentralized-github) (GitHub alternative), [Infinipedia](https://github.com/bitsocialnet/infinipedia) (encyclopedia), [prediction-markets](https://github.com/bitsocialnet/prediction-markets) (prediction markets with Bitsocial discussion)
