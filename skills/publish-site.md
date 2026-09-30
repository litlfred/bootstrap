---
name: publish-site
description: >
  Give an instance a site on GitHub Pages: a workflow that builds its READMEs
  into one page with a table of contents and deploys it, Pages switched on and
  built by that workflow, and the address answering. It switches Pages on
  only with an authenticated forge CLI, and otherwise hands the person the
  one step.
---

# The site: a workflow, Pages on, the address answering

**The site is the vehicle, not the goal.** The goal is the harness's JSON
Schemas and JSON-LD answering at the IRIs they name
([`publish-documents`](publish-documents.md), the primary step); the site is
how they get there, and a README page for people rides along.

A declaration that names a `repository` on GitHub has a site address:
its `iriBase` when it declares one, otherwise
`https://<owner>.github.io/<repo>/`. **An address a declaration names and
that answers 404 is a broken promise**, and every IRI minted under it
breaks with it. So the site is three initialization steps
([`initialization-steps`](initialization-steps.md)), each checked before
anything is done.

## `site:workflow`: a workflow deploys it

A workflow under `.github/workflows/` that uses `actions/deploy-pages`.
Bootstrap's own [`pages.yml`](../.github/workflows/pages.yml) is the pattern.
On a push to `main` (or by hand) it checks out the instance and its toolset
side by side, **stages** the site, builds it with Jekyll, and deploys it with
`actions/configure-pages`, `actions/upload-pages-artifact` and
`actions/deploy-pages`.

What the staged site holds:

- **the index page is every README as one document**: the root README
  first, then each directory's README in path order, with a table of
  contents, each README's headings demoted under its own section, and every
  relative link rewritten so it still lands;
- **every JSON Schema and JSON-LD document at the IRI it names**, with a
  `.json` copy beside each `.jsonld`, listed under "Published documents" on
  the index page ([`publish-documents`](publish-documents.md));
- every file of the instance, as it sits, so every link into it resolves;
- for bootstrap, its own Knowledge Graph at the address its `@id` names
  ([`bootstrap-graph-publication`](bootstrap-graph-publication.md)).

Adding the workflow replaces nothing. If one already deploys to Pages,
the step is done.

## `site:enabled`: Pages is on, built by the workflow

Pages must be switched on **with "GitHub Actions" as its source**, or the
workflow's deploy is refused.

- **With an authenticated `gh`**: check with `gh api repos/<owner>/<repo>/pages`.
  If it answers 404, Pages is off: switch it on with
  `gh api -X POST repos/<owner>/<repo>/pages -f build_type=workflow`, then
  check again. If Pages is on but built from a branch, **do not switch it
  yourself**: that changes how an existing site is built, so ask.
- **Without `gh`, or not signed in**: the step is *could not determine*, and
  it goes to the person through
  [`human-agent-discussion`](human-agent-discussion.md), written so it can
  be followed without opening anything else:

  > Open `https://github.com/<owner>/<repo>/settings/pages`. Under "Build and
  > deployment", set **Source** to **GitHub Actions**. It is free for a
  > public repository. Then re-run the Pages workflow from the Actions tab,
  > or push to `main`.

Never silent: a step that could not be checked is said to be unchecked.

## `site:live`: the address answers

Fetch the address. 200 is done. 404 after Pages is on usually means the
workflow has not run since; the run is at
`https://github.com/<owner>/<repo>/actions`. A 403, 407 or 5xx, or no answer
at all, is about the way from here (a proxy, an access rule), not about the
site: *could not determine*, never done and never not done.

## Cost

Pages on a public repository, and the Actions minutes its workflow uses, are
free. Nothing else is switched on.
