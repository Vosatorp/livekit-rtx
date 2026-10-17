# Narrow RTX compatibility fork

This repository snapshots the **published `livekit 0.8.3` crate**, not the whole
Rust SDK workspace. The normalized published manifest intentionally keeps sibling
dependencies in crates.io; it avoids switching the entire SDK dependency graph
to Git. The initial commit is the unchanged archive named in
`RTX_PATCH_PROVENANCE.json`. Apache-2.0 licensing and upstream attribution remain.

The only runtime change is three added lines in `create_sender`: retain the
advertised `video/rtx` capability when selecting the preferred video codec.
No retry policy, timeout, keyframe scheduling, encoder selection, encryption,
bitrate or room lifecycle is changed. Supplemental codecs are not fabricated.

Consumers must pin a **full commit SHA**, keep Cargo.lock, and verify the source
hash from the provenance file. Never patch a global Cargo cache or use a mutable
branch as a release dependency. The consuming native interoperability regression
must assert a negotiated RTX SSRC after video encoding starts; packet-loss tests
must separately assert actual decoded/presented frame freshness, not just input
enqueue counters or successful connection.

This is not an independent feature branch. On each SDK update:

1. Check whether upstream retains RTX in codec preferences.
2. Prefer the official package once it contains the correction.
3. Otherwise import and verify the new published archive, reapply only this
   semantic change and repeat native negotiation/E2EE/packet-loss tests.
4. Update the consumer's exact SHA and lock together. Do not silently rebase or
   move an already-used pinned commit.

Local QA does not prove a managed LiveKit Cloud build contains its server-side
RTX feedback correction. It also does not establish arbitrary-loss resilience.
