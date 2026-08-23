# The 24th Group — Product Repository Standard

## Purpose

Every product repository should contain enough structured context for a monthly review to understand what the product is, where it is going, what changed and what is blocked without having to reconstruct the project from commits alone.

This standard should stay lightweight.

## Minimum files

Each active product repo should contain the following files at the root or in a clearly named project-management folder.

### `README.md`
Explain:
- what the product is
- who it is for
- the current architecture at a useful level
- how to run or test it
- where important code lives

### `CONTEXT.md`
Explain:
- product purpose
- positioning
- current product boundaries
- important decisions or assumptions
- what the product is deliberately not trying to become

### `ROADMAP.md`
Keep a short list of:
- current strategic objective
- next major milestones
- major dependencies
- what success looks like for the current phase

Do not duplicate the full Asana backlog.

### `STATUS.md`
A concise current-state snapshot containing:
- current phase
- what is working
- what is not yet proven
- known blockers
- most important next step
- date last updated

This is the fastest file for a monthly review to read before inspecting commits and Asana.

## Optional files

Add these only where useful:

- `DECISIONS.md` for important product decisions
- `ARCHITECTURE.md` for systems that need deeper technical explanation
- `CHANGELOG.md` when release history is not already obvious from GitHub releases/commits
- `CUSTOMER-DISCOVERY.md` where discovery is a major part of the current phase
- `RELEASE.md` or deployment notes where production traceability matters

## Source-of-truth rules

- **Asana** answers: what is being worked on, who owns it, what is blocked?
- **GitHub** answers: what changed in the implementation?
- **STATUS.md** answers: what is the current product state?
- **ROADMAP.md** answers: where are we trying to get next?
- **CONTEXT.md** answers: why does this product exist and what are its boundaries?

If these sources disagree, do not silently reconcile them. Flag the inconsistency in the monthly review.

## Update expectations

The files do not need constant maintenance.

At minimum:
- update `STATUS.md` when the product meaningfully changes state
- update `ROADMAP.md` when milestones or priorities change
- update `CONTEXT.md` when positioning or product boundaries change
- keep `README.md` accurate enough for a new contributor to orient themselves

## Monthly review readiness checklist

Before the Group monthly review, each active product should ideally have:

- [ ] current Asana tasks/status
- [ ] recent GitHub work committed
- [ ] `STATUS.md` not materially stale
- [ ] roadmap still reflects current priorities
- [ ] blockers/dependencies explicitly recorded
- [ ] major decisions documented if they changed direction

## Naming and style

- Use plain Markdown.
- Prefer short sections and direct language.
- Use dates in `YYYY-MM-DD` format where dates matter.
- Avoid investor language unless the document is specifically for that purpose.
- Do not turn documentation into a second project-management system.

## Adoption

Apply this standard gradually. Existing repos do not need to be restructured all at once. The priority is making the active products easier to review reliably each month.
