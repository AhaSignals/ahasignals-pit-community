# Report a problem or describe an integration

Use [AhaSignals PIT community issues](https://github.com/AhaSignals/ahasignals-pit-community/issues/new/choose).
For an initial inquiry that cannot be public, email research@ahasignals.com
with a description first. Do not send credentials or a private dataset.

## Minimum useful report

1. Package version, Python version and operating system (no machine paths).
2. Ruleset and mode; explain what the cutoff means in your research.
3. The smallest permitted input reproducing the issue, preferably a synthetic
   modification of an installed example. Preserve the conditions causing the
   finding when replacing identifying values.
4. Actual gate, rule ID, reason and expected result with your reasoning.
5. The exact command using relative example filenames.

Do not post API keys, local home-directory paths, private URLs, personal email
addresses, customer information or data you cannot redistribute. Reports can
contain revealing dataset/row identifiers even when they omit raw amounts.
Review both input and output before posting. An input hash alone cannot reproduce
an issue; explain the structure and finding if you cannot share the input.

## Integration requests

Describe the data source's field structure, the stage where checks would run,
which evidence you retain, and what makes a result useful. Missing fields are
useful feedback. Do not synthesize acceptance or observation timestamps to get
a passing result. A sample column list is enough for the first discussion.

After a fix, rerun the same permitted input on the stated release and report
whether the issue is resolved. A minimized example may become a regression test
only when its license or your permission allows redistribution. Private reports
will not be converted into public cases without permission.
