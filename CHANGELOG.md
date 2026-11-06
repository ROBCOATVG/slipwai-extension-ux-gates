# Changelog

Release notes, newest first, one entry per released version; the entry being written is `changelog.d/`.

## 1.0.1

**The render gates run where the extension is adopted, not only where a project happened to add Playwright.** The kit's render gates import Playwright and inject axe-core, and the kit declares neither: a project that never added Playwright itself had render gates that reported *skipped, not passed* on every run, and `make verify` green throughout — recap's, for fifteen days, while 22 of its screens ran 91px off a 280px phone. Adopting the extension now installs Playwright 1.64.1 and axe-core 4.10.2 into the kit's own `node_modules`, ignored by Git with the kit, and Playwright's Chromium, so the project's manifest is never touched. The same pre-fix checkout of recap, with only this extension, failed the overflow gate in 43 s.

**The CI job runs the gate that exists.** The `ux-gates` job the extension writes into `verify.yml` ran `scripts/check-ux-gates.py`, which no checkout has had since the gate moved to `scripts/extensions/ux-gates/hooks/check.py`; every shard failed with *No such file or directory*. It runs the hook now, and installs Chromium from the kit's directory, for the kit's own Playwright.

**The render gates open Playwright's Chromium first, so a full run takes minutes rather than most of an hour.** Every per-file gate opens and closes a browser of its own, four per preview, and the kit asks for an installed Chrome; on macOS that Chrome took 10 s to close, so recap's 528 per-file gates spent about 88 minutes of a run waiting for it. The probe now asks for Playwright's Chromium first — the browser CI already measures on — and an installed Chrome only where Playwright's is not installed. The same gate went from 11.5 s to 1.1 s with the same verdict; recap's full run from about 1 h 40 m to 9 m 23 s on one browser, and 2 m 28 s on four.

**A branch of its own renders only what it changed, as a captain's session already did.** With neither `UX_GATES_SINCE` nor `SLIPWAI_TRUNK` set, a checkout on a branch other than trunk (`origin/HEAD`, or `main`) is scoped to what changed since trunk: a slice's `make verify` run by hand with one preview changed took 3 s. Trunk itself, a detached HEAD — which is what CI's push to `main` checks out — and any run under `CI` still render every preview, so nothing that deploys is scoped.

**A slice's verify renders on its share of the machine's browsers, and only the previews the slice changed.** Where slipwai's captain started the session, the render gates take `SLIPWAI_BROWSERS` as their default `UX_GATES_JOBS` and `SLIPWAI_TRUNK` as their default `UX_GATES_SINCE`. Several slices verifying on one machine each ran a browser per processor over every preview in the project: hr-test-2 had twenty Chromes open for one slice, at a load of two hundred, and every slice re-checked every other slice's screens. A slice is on its way to trunk as a pull request is, so it is scoped as one; trunk's own run and `verify.yml` still render everything. Either switch set by hand still wins.

## 1.0.0

**ux-gates ships as an extension package: `slipwai extension install ux-gates`, elected in a project with
`./init --extension ux-gates`.** The two UI review gates a project elects, with the check that keeps what
they write current, and a missing tool reported rather than fatal to `./init`.
