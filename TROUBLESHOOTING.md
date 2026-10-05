# Interpret a financial input finding

Start with the report's `gate`, then inspect `checks`: each item has `rowId`,
`ruleId`, `verdict`, `reason`, evidence references and `remediation`. Keep the
original input; evidence references resolve against its facts.

| Finding | Inspect | Next action |
| --- | --- | --- |
| Acceptance after cutoff | Selected filing acceptance and original decision time | Select an eligible earlier fact with the intended context, or exclude the feature. Keep the original cutoff. |
| Observation after cutoff | Your pipeline's retained acquisition record | Exclude data acquired too late. Do not backdate receipt. |
| Observation before acceptance | Meaning, timezone and source of each clock | Correct a demonstrable mapping error using evidence; retain the original record. |
| Missing observation time | Historical collection logs or snapshots | Supply retained evidence or keep UNKNOWN. A separately declared disclosure reconstruction answers a weaker question. |
| Context mismatch | Issuer, literal concept, unit, start, end and dimensions | Choose the fact matching the intended feature. Quarter and YTD are not interchangeable. |
| Stale or ambiguous selected version | All supplied eligible versions and conflicts | Inspect the candidate history. Choose an unambiguous eligible version under the declared policy or withhold the feature. Do not delete conflicting evidence to force PASS. |
| Missing provenance | Accession, context ID, URL and document hash | Recover from permitted source documents and acquisition records; retain UNKNOWN when unavailable. |
| ERROR | Report `errors`, JSON structure, field types and input path | Correct the transport or schema issue, then rerun. ERROR gives no financial verdict. |

## Common input mistakes

- A date-only `filing_date` is not a precise acceptance timestamp. Do not invent midnight precision.
- Nanosecond precision is supported, not required. A timestamp such as `2025-05-01T20:00:00Z` is accepted. Supply the precision and timezone actually supported by your evidence.
- Computing a document hash today identifies today's bytes. It cannot establish historical acquisition.
- `contextId` belongs to a document's XBRL context; do not construct it from a ticker and date.
- A missing period start is unknown. An empty start explicitly declares an instant fact.
- `expected` must describe your intended research feature. Copying it from a selected fact can conceal a wrong selection.

## Moving the cutoff is a new experiment

If the research decision was June 30 and the fact was accepted in August, moving
the cutoff to August does not repair the June experiment. Preserve June's cutoff
and select an eligible fact or withhold the feature. If you deliberately delay
the strategy decision, document that change and rerun the affected experiment.

## What PASS does not establish

The five rules check supplied declarations and the supplied candidate set.
They do not prove the source is authentic or the history complete. This version
uses the latest eligible declared candidate; a first-reported-value strategy
needs a different explicit policy. The tool does not validate restatement
meaning, market prices, survivorship, model memory or investment performance.

If a result seems incorrect, prepare a [minimal reproducible report](FEEDBACK.md).
Research and education only; no backtest certification or investment advice.
