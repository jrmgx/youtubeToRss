# YouTube channel RSS (system playlists)

<a href="https://jrmgx.github.io/youtubeToRss/">Live at: jrmgx.github.io/youtubeToRss</a>

A small static page (`index.html`) that builds **YouTube Atom/RSS feed URLs** for a channel using **internal “automatic” playlist IDs** derived from the channel ID. You pick what the feed should contain (everything, long-form only, Shorts, popular lists, members-only variants, and so on).

## Concept

YouTube’s default channel feed uses:

```text
https://www.youtube.com/feeds/videos.xml?channel_id=UCxxxxxxxxxxxxxxxxxxxxxx
```

That feed follows **all** uploads in one stream. For finer control, people use the fact that YouTube also maintains **system playlists** tied to each channel. Those playlist IDs are not random: for a normal channel ID (24 characters, starting with **`UC`**), you can form a playlist ID by **replacing the leading `UC` with another prefix** (two or more letters). You then request the same feed endpoint with **`playlist_id`** instead of **`channel_id`**:

```text
https://www.youtube.com/feeds/videos.xml?playlist_id=<PREFIX><22 characters after UC>
```

Example: if the channel ID is `UC` + `xxxxxxxxxxxxxxxxxxxxxx`, choosing prefix **`UULF`** yields a playlist ID `UULF` + `xxxxxxxxxxxxxxxxxxxxxx` and an RSS URL that tends to reflect **long-form uploads only** (no Shorts), which is a common use case for podcast-style readers.

This behavior is **documented in the community** (see references), not as a guaranteed public API. Prefixes and feed contents may change without notice.

## What the page does

1. You paste a **channel URL** containing `/channel/UC…` or the **raw channel ID** (`UC` + 22 characters).
2. You choose a **feed type** (dropdown), which maps to a **prefix**.
3. The page computes `playlist_id = channelId.replace(/^UC/, prefix)` and shows the full `feeds/videos.xml?playlist_id=…` URL for copying into an RSS reader.

The page validates that the channel ID looks like a standard `UC…` id (24 characters total).

## Getting the channel ID from a handle-only URL

Handles look like `https://www.youtube.com/@kurzgesagt`. This tool does **not** resolve handles to IDs (that would need a server or YouTube API).

To get the numeric channel ID:

1. Open the channel page.
2. Under the channel header, open **… more links** (expand the links section).
3. Scroll to the bottom.
4. Use **Share channel** → **Copy channel ID**.
5. Paste that `UC…` value (or a full URL that contains `/channel/UC…`) into the tool.

YouTube may tweak labels or layout over time; the goal is always to obtain the **`UC` channel ID** string.

## Prefix reference

| Prefix | Typical meaning |
|--------|-----------------|
| `UU` | Latest: all public uploads (videos + Shorts + live) |
| `UULF` | Latest: public long-form videos only (no Shorts) |
| `UUSH` | Latest: Shorts only |
| `UULV` | Latest: live streams only |
| `UULP` | Popular: videos (~200, descending) |
| `UUPS` | Popular: Shorts |
| `UUPV` | Popular: live streams |
| `UUMO` | Latest: members-only (all formats) |
| `UUMF` | Latest: members-only videos |
| `UUMS` | Latest: members-only Shorts |
| `UUMV` | Latest: members-only live streams |

**Members-only** feeds only expose what the requesting account could watch; a generic RSS client is usually **not** logged in as a member, so those feeds are often empty or useless outside special setups.

## Caveats

- **Unofficial:** These prefixes are reverse-engineered / community-summarized, not part of the [YouTube Data API](https://developers.google.com/youtube/v3) contract.
- **Stability:** YouTube may change IDs or feed behavior; do not rely on this for critical production without monitoring.
- **Members:** Prefer official API + OAuth if you need reliable access to member content.

## References

- Community summary of playlist ID prefixes: [Stack Overflow — “YouTube channel and playlist ID prefixes”](https://stackoverflow.com/a/77816885)
