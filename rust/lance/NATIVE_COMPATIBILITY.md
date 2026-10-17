# Lance 9 native compatibility build

Base: upstream `v9.0.0`, commit
`7653c2065d6951e0ef236ba9587c6832f7456947`.

The scanner prefilter uses the complete fragment union of the selected logical
FTS index. Native FTS execution already reads all committed index segments;
restricting the prefilter to its first segment drops filtered delta hits.
Unknown coverage retains the existing all-current-fragments fallback. This
changes neither the file format nor the query's filtering-before-top-K contract.

`test_fts_prefilter_covers_all_committed_segments` exercises Match, Phrase,
MultiMatch and Boolean queries over a retained base plus a committed delta,
with V1 and V2 index formats, both filter ranges, and scoped top-K retrieval.

The package manifest and materialized `protos/` inputs are byte-equal to the
published `lance` 9.0.0 crate. This makes the patched package self-contained for
Cargo and Bazel, retaining registry identities for its sibling Lance 9 crates.
It avoids mixing Git and registry copies of shared Arrow/Lance types. All other
upstream source and dependency versions remain unchanged. Consumers must pin
an immutable commit and qualify their actual feature and platform surface.

Qualification of this backport: all eight focused scanner regressions pass.
Replacing only the coverage helper with the released implementation makes the
same eight regressions fail; restoring it makes all eight pass again.
Strict minimal-feature library/test Clippy on Rust 1.98.1 reports existing
upstream unused test imports and `chunks_exact_to_as_chunks` findings in
unchanged files. Those findings are not suppressed or claimed as a lint pass.
The changed scanner passes Rust 2024 formatting validation.
