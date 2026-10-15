# FarmTrack M0 testing and staging policy

## Baseline
- Production: `main` deployed by `.github/workflows/pages.yml` to the existing GitHub Pages site.
- Isolated development: `m0-finance-regression-tests`. It is **not** a production deployment source.
- Local device data (`localStorage` key `farmtrack_v1`) belongs to the user's browser/PWA, not GitHub. Git version rollback cannot restore or overwrite device records.

## Test execution
- `.github/workflows/m0-tests.yml` runs Node 22 tests on M0 branch pushes and pull requests into `main`.
- Tests parse all inline scripts and exercise expense/sale reconciliation against synthetic data.
- A passing run packages a `farmtrack-staging-candidate` Actions artifact retained for 14 days.
- An artifact is **not a publicly hosted staging URL**. Do not test on the live `vaishnavihsalunkhe.github.io/FarmTrack/` origin until a separate staging origin is provisioned.
- Never set the GitHub Pages source for this repository to the M0 branch. It would replace production.

## Staging provisioning (next infrastructure milestone)
1. Choose an independent staging origin or repository with access controls suitable for the pilot.
2. Deploy only from the M0 branch after checks pass.
3. Use synthetic test data; do not import identifiable production farmer records into staging.
4. Verify navigation, forms, backup/restore, offline behavior, service-worker upgrades and finance totals on Android Chrome.
5. Record the tested commit SHA and bundle hash before any release decision.

## Financial scenarios
- Empty farm ledger.
- Ordinary expenses and mixed categories.
- Sale event amounts excluded from expenses.
- Legacy sale category without linked event.
- Legacy event-linked sale expense (no double counting).
- Fractional rupee calculations.
- Sale amount revisions.
- Existing data stays unchanged during read-only analysis.

## Release gates
- Full GitHub Actions test result is green.
- Staging UI interactions and offline tests pass on a real browser.
- Financial totals reconcile for migration fixtures.
- Exported backup validated and restoration exercised using synthetic records.
- Explicit approval for production release.

## Rollback
1. Stop any pending rollout.
2. Identify last known-good deployed commit and redeploy it via approved production release process.
3. Avoid clearing browser storage, unregistering service workers or reinstalling as a first response: those steps can affect locally stored farm data.
4. Preserve/export user data first, then investigate any migration failure.
5. Verify page load, navigation and record retrieval after rollback.

## Current limitations
- Tests exercise selected real calculation functions, but do not constitute end-to-end browser testing.
- No independent staging URL has yet been configured.
- Backup import presently needs schema validation before being trusted with user records.
