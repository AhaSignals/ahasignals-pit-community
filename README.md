# Start using AhaSignals PIT

These instructions use the published **0.2.0rc1** release candidate. Python 3.9
or later is required. Checks run locally without network access, telemetry,
an account or runtime dependencies. Installation and optional downloads use
the network. Commands below use a macOS/Linux shell; choose an empty directory
because shell redirection overwrites files.

## 1. Install from PyPI

```bash
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install "ahasignals-pit==0.2.0rc1"
python3 -m ahasignals_pit.audit_cli --version
```

Expected version: `0.2.0rc1`. The explicit version selects this pre-release;
an unpinned installation may select an earlier stable release. No access to
the development repository is needed.

## 2. Create an input and save your first report

```bash
python3 -m ahasignals_pit.audit_cli example pass > audit-input.json
python3 -m ahasignals_pit.audit_cli check --input audit-input.json > audit-report.json
```

Expected: exit code **0**, report `gate: "PASS"`, five applicable checks.
The example is **fully synthetic**, including its source identifiers and zero
document hash. PASS checks the supplied declarations; it does not authenticate
them. An installed package does not create input files until you run the
example command and redirect its output.

The same input works with the Python API:

```python
import json
from pathlib import Path
from ahasignals_pit import audit_batch

payload = json.loads(Path("audit-input.json").read_text(encoding="utf-8"))
report = audit_batch(payload)
Path("api-report.json").write_text(json.dumps(report, indent=2), encoding="utf-8")
print(report["gate"])
```

Expected output: `PASS`. The CLI additionally records hashes of the original
input bytes; the API report hashes the canonical input. Keep the input and report
together in your own storage.

## 3. See how failures and missing evidence behave

```bash
python3 -m ahasignals_pit.audit_cli example fail > future-input.json
python3 -m ahasignals_pit.audit_cli check --input future-input.json > future-report.json
```

The check intentionally exits **1** with `gate: "FAIL"`. For missing evidence:

```bash
python3 -m ahasignals_pit.audit_cli example unknown > unknown-input.json
python3 -m ahasignals_pit.audit_cli check --input unknown-input.json > unknown-report.json
```

The check intentionally exits **3** with `gate: "UNKNOWN"`. These nonzero exits
are expected findings, not installation failures. Invalid or unreadable input
exits **2** (`ERROR`). Only exit **0** permits a job requiring these checks to
continue. See [what each finding means](TROUBLESHOOTING.md).

## 4. Run a case using real disclosure facts

```bash
curl --fail --location https://ahasignals.com/examples/financial-batch-audit/v1/ytd-mismatch.json --output ytd-mismatch.json
python3 -m ahasignals_pit.audit_cli check --input ytd-mismatch.json > ytd-report.json
```

Expected exit **1**: `PIT-CONTEXT-001` reports `context-mismatch`. The requested
April–June operating income is $19,209,000, but the selected January–June fact
is −$8,261,000. These figures come from CoreWeave's filing for June 30, 2025.
The intentionally wrong selection demonstrates a period mismatch; it is not
an observed error in an external team's backtest or evidence of return impact.

Read the [source ledger and four complete cases](https://ahasignals.com/examples/financial-batch-audit/v1/README.md)
for source URLs, document hashes, eligible versions and missing observation
evidence. The checker does not download or authenticate the cited documents.

## 5. Connect your own data

The existing [batch contract](https://ahasignals.com/examples/ahasignals-pit/v0.2.0rc1/BATCH-AUDIT.md)
defines fields, JSONL input, modes and rules. Its release-time installation text
predates PyPI publication; use the pinned PyPI command above.

1. Choose a dataset ID and snapshot version you can retrieve again.
2. Preserve all supplied candidate versions in `facts`, with stable fact IDs.
3. In `rows`, declare the selected fact, original decision cutoff and intended
   financial context. Derive `expected` from your research question, not from
   the selected fact you are trying to check.
4. Choose `observed-pipeline` only with this pipeline's retained acquisition
   evidence. `disclosure-reconstruction` checks declared public acceptance and
   explicitly excludes historical acquisition. Changing modes changes the claim.
5. Inspect failures and unknowns before permitting downstream work.

The [annual-record adapter](https://ahasignals.com/examples/ahasignals-pit/v0.2.0rc1/EXTERNAL-INTEGRATION.md)
supports one documented extractor structure. It does not infer precise clocks
from filing dates. Missing evidence remains unknown. The compatibility example
does not establish adoption or endorsement by that project's maintainer.

## 6. Repeat the check in CI

Use the [CI guide](CI.md) and [downloadable workflow](financial-input-audit.yml).
The workflow checks a real input file you supply; it does not replace missing
input with a passing fixture. It saves the JSON report before enforcing the
gate. Input/report uploads are **off by default**; review their contents and
repository visibility before enabling report artifacts.

For repeat runs, retain the exact input, tool version, ruleset, mode and report.
A different dataset, cutoff, mode or rule version changes the comparison.
Removing failed rows or narrowing checks is not evidence of improved quality.

## Source, scope and feedback

- [PyPI release and files](https://pypi.org/project/ahasignals-pit/0.2.0rc1/#files)
- [MIT-licensed source and tests](https://ahasignals.com/examples/ahasignals-pit/v0.2.0rc1/ahasignals_pit-0.2.0rc1.tar.gz)
- [Reviewed archive member hashes](https://ahasignals.com/examples/ahasignals-pit/v0.2.0rc1/release-audit.json)
- [Report an issue or request an integration](https://github.com/AhaSignals/ahasignals-pit-community/issues/new/choose)
- [Prepare a minimal feedback example](FEEDBACK.md)

The public source archive contains this Python package and its tests. The
development repository is private. For a confidential initial inquiry, contact
research@ahasignals.com; start with a description, not an attached private dataset.

The checker tests supplied fields and candidate versions. It cannot establish
source authenticity, complete history, restatement meaning, price integrity,
survivorship, execution costs, model-memory leakage or strategy profitability.
AhaSignals is independent of the referenced issuers, regulators and software
platforms. Research and education only; no investment advice or certification.
