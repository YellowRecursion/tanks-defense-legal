# Tanks Defense Legal

Official legal documents and version history for Tanks Defense.

## Current documents

- [Terms of Service](https://tanksdefense.com/terms)
- [Privacy Policy](https://tanksdefense.com/privacy)
- [Purchase Terms](https://tanksdefense.com/offer)

Current versions and paths are listed in [`manifest.json`](manifest.json).

Published versions are immutable. Changes to a published document are released as a new version. The Russian text is the original; translations may be added alongside it in the corresponding version directory.

## Developer guide

[`manifest.json`](manifest.json) is the source of truth for current document versions and file paths. Consumers must not determine the current version by scanning directories or hard-code a versioned path. The game and website should receive legal documents through the Tanks Defense backend; the backend reads this repository and keeps the last valid snapshot in its cache.

### Manifest fields

- `schemaVersion` — version of the manifest format. Increment it only when the structure or meaning of the manifest itself changes.
- `documents` — documents indexed by stable technical identifiers: `terms`, `privacy`, and `offer`.
- `currentVersion` — latest published version of a document. Its file is stored in the corresponding `vN` directory.
- `effectiveAt` — date and time from which the current version applies, in ISO 8601 format with a time-zone offset.
- `locales` — mapping from a locale code to the Markdown file of the current version.
- `requiredAcceptanceVersion` — latest Terms of Service version that requires explicit acceptance by the user. This field is used only for `terms`, never decreases, and must not be greater than `currentVersion`.

`requiredAcceptanceVersion` is separate from `currentVersion` because not every published change requires another confirmation. For example:

- version 1 is initially published: `currentVersion = 1`, `requiredAcceptanceVersion = 1`;
- version 2 only fixes wording or formatting: `currentVersion = 2`, `requiredAcceptanceVersion = 1`;
- version 3 materially changes user rights or obligations: `currentVersion = 3`, `requiredAcceptanceVersion = 3`.

The game must request explicit acceptance when the version previously accepted by the user is lower than `requiredAcceptanceVersion`:

```text
acceptedTermsVersion < requiredAcceptanceVersion
```

The Privacy Policy does not use this field: a separate notice or consent is requested when required by law. Purchase Terms are accepted for each purchase, and the version applicable to that purchase should be recorded with the payment.

### Publishing a new version

1. Create a new `documents/<document>/vN/` directory. Never overwrite a published file.
2. Add the original Russian document as `ru.md`. Add translations to the same version directory using their locale codes, for example `en.md`.
3. In the same commit, update `currentVersion`, `effectiveAt`, and every applicable path in `locales`.
4. For a material Terms of Service change requiring explicit acceptance, also set `requiredAcceptanceVersion` to the new `currentVersion`. Leave it unchanged for editorial or technical changes.
5. Before publishing, validate the JSON and ensure every path referenced by the manifest exists and points to a non-empty file.

If the manifest or any referenced document is invalid or unavailable, the backend must continue serving its previous valid cached snapshot.
