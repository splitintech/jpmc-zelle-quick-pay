# Standalone migration

Canonical repository: https://github.com/splitintech/jpmc-zelle-quick-pay

Extracted from `4e8b11a089820b5b9fa4f409cda74ec4ac7da34c` in splitintech/open-internal-tools. The product directory is now the repository root. Relevant history, authors, modes, assets, tests, documentation and release tooling are preserved. The [commit map](migration/commit-map.txt) maps upstream IDs to filtered history; signed upstream tags remain in the hub and are not recreated.

[Original hub inventory](migration/hub-source-files.json), [selected source inventory](migration/source-files.json), [migration snapshot](migration/snapshot.json), and [validation](migration/validation.json) provide file-level provenance and explain intentional changes. Run `python3 scripts/check-migration.py --snapshot` to verify the exact migration.

Migration creates a source repository, not an npm release, live deployment, provider qualification, database rollout or credential transfer. Roll back consumers to their previous artifact or use the immutable upstream revision. The original feature branches are retained as historical sources.
