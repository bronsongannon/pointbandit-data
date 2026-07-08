# pointbandit-data

Public data endpoint for the **Point Bandit** app. Holds only publicly-available
transfer-bonus data — no app source, no secrets.

## `transfer_bonuses.json`

The live list of active credit-card point-transfer bonuses. The app fetches it at:

```
https://raw.githubusercontent.com/bronsongannon/pointbandit-data/main/transfer_bonuses.json
```

Set as `TRANSFER_BONUSES_URL` in the app's `app/config.ts`. On any fetch failure
(404, offline, timeout) the app falls back to the JSON bundled in that build, so
this endpoint is a **freshness layer, never a hard dependency**.

Shape (matches `TransferBonusesFile` in `app/hooks/useTransferBonuses.ts`):

```jsonc
{
  "lastUpdated": "2026-07-06T00:00:00Z",   // ISO timestamp
  "source": "…",                            // where the data was scraped from
  "bonuses": [ /* TransferBonus[] */ ]
}
```

## How it's updated

The weekly `transfer-bonus-refresh` pipeline in the main app repo promotes
corroborated bonuses into `app/data/transfer_bonuses.json` and pushes the same
file here (see `pipeline/transfer_bonus_refresh.agent.md`). Publishing here is
what lets bonus refreshes reach users **without an app rebuild**.

Only public, non-targeted offers are ever published.
