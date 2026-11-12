# A survey workspace for QREtools

A workspace holds survey questions and their documentation, one YAML file per question.
It also holds instruments built from those questions, one YAML file each. QREtools turns
both into DDI-Lifecycle 4.0.

What QREtools is and how it works: [About QREtools](https://github.com/JHUCities/qretools#readme).

This repository is the template for new workspaces:
<https://github.com/JHUCities/qretools-template>. Start one with
[Use this template](https://github.com/JHUCities/qretools-template/generate).
It has example questions in `questions/example`, and no instrument. When you start your
first instrument (New → Instrument…), QREtools offers an example instrument that asks
these questions.

## Set up a new workspace from this template

1. **Create your workspace:** "Use this template" → "Create a new repository". Steps 3
   and 4 need a public repository, or a private repository on a paid GitHub plan. On the
   free plan, GitHub does not allow rulesets in a private repository.
2. **Install the QREtools app** on the new repository:
   <https://github.com/apps/qretools/installations/new> → choose your account or
   organisation → "Only select repositories" → this one. Without it you can read the
   workspace in QREtools but you can't save.
3. **Protect `main`:** Settings → Rules → Rulesets → New branch ruleset. Target the
   default branch; turn on "Require a pull request before merging", "Block force
   pushes" and "Restrict deletions". QREtools saves each person's work to their own
   branch (`qretools-<login>`). The workspace changes only when a pull request is merged.
   Also turn on "Require status checks to pass" and add the check named `check`. Without
   it, a pull request that would bring an instrument that isn't ready to release into
   `main` can still be merged (see "Instruments").
4. **Protect release tags:**
   1. Go to Settings → Rules → Rulesets → New ruleset → New tag ruleset.
   2. Type a ruleset name, for example "Protect release tags".
   3. Set "Enforcement status" to "Active".
   4. Click "Add target".
   5. Click "Include by pattern".
   6. Type `v*`.
   7. Turn on "Restrict updates".
   8. Turn on "Restrict deletions".
   9. Turn on "Block force pushes".
   10. Do not add an actor to the bypass list.
   11. Click "Create".

   With this ruleset, no person can move or delete a release tag. Because of this, an
   instrument always reads the same questions at the same tag. The Release workflow
   only creates tags, so the ruleset does not stop the Release workflow.
5. **Tidy merged branches:** Settings → General → Pull Requests → "Automatically delete
   head branches".
6. **Set your agency:** in `workspace.yaml` (the gear beside the workspace's name in
   QREtools), replace `org.example` with your DDI agency: letters, digits and hyphens,
   in parts joined by dots, such as `edu.example.survey-lab`. Until you do, anything you
   export is published under `org.example`, which isn't yours.
7. **Publish the DDI:**

   **Warning: the site is public, also for a private repository. Do not do this step if
   your questions must stay private.**

   1. Go to Settings → Pages.
   2. Under "Build and deployment", set "Source" to "GitHub Actions".

   After each merge to `main`, the DDI workflow (`.github/workflows/ddi.yml`) publishes
   the DDI of `main` at `https://<owner>.github.io/<name>/ddi.json`. It also publishes
   each release at `https://<owner>.github.io/<name>/<tag>/ddi.json`. To keep a release
   off the site, write its tag on a line in `.github/pages-skip`.
8. **Open it** in QREtools: sign in at <https://qretools.com> and enter the repository
   as `owner/name`.

## From a terminal

`mise.toml` pins Node and `qretools`, QREtools' command-line tool. With
[mise](https://mise.jdx.dev), run `mise install`. Then `qretools check .` lists what's
to fill in or fix, as QREtools shows it, and `qretools export . -o ddi.json` writes the
DDI. Without mise: `npx qretools@<the version in mise.toml> check .`. To upgrade, change
the version in `mise.toml`.

`.github/workflows/check.yml` runs the same check on every pull request and every push
to `main`. It fails if anything is left to fill in or fix, and marks each error and
warning on its line in the pull request. To fail on warnings too, make its last line
`qretools check . --strict`. (`release.yml` is covered under "Releasing this workspace".)

## What's where

| File or folder | Holds |
|---|---|
| `mise.toml` | the pinned tools: Node and `qretools` (see "From a terminal") |
| `workspace.yaml` | the workspace's settings: its DDI agency (`agency:`), fields every question must have (`required:`), extra fields for one folder (`required_in:`), and the tag locks QREtools writes for you (`locked:`) |
| `questions/<folder>/<name>.yaml` | one question each; you choose the folder when you first save a question |
| `concepts/<name>.yaml` | shared concepts (`label:`, `definition:`), named from `concept:` |
| `units/<name>.yaml` | shared units (`label:`, `definition:`), named from `unit:` under `number:` |
| `scales/<name>.yaml` | shared response scales (`labels:`), named from `responses:` |
| `universes/<name>.yaml` | shared universes (`text:`), named from `universe:` |
| `instructions/<name>.yaml` | shared instructions (`text:`), named from `instruction:` |
| `missing.yaml` | the missing-value codes (`labels:`) |
| `select_all.yaml` | how a select all that apply option is stored (`chosen:`, `not_chosen:`). This template has none, so it's 1 Yes and 0 No |
| `instruments/<name>.yaml` | one instrument each (none yet: QREtools makes the first) |

Each question name is used once in the workspace, whatever its folder. So is each shared
file's name within its kind. The codes in `missing.yaml` are only an example: use your
own.

`questions/example/` is the one folder name QREtools looks for. The example instrument
asks the questions in it, and is offered only while they're saved there. Put your own
questions in folders of your own, and delete this one when you no longer need the
example. Keep the shared files it uses (scales, universes, units) if you like them.

## Shared values, and avoiding duplicates

Six things can be written once and named from any question:

| Shared | Named from | Example here |
|---|---|---|
| a concept (what is measured) | `concept: service_satisfaction` | `concepts/service_satisfaction.yaml` and three more |
| a unit | `number: { unit: days }` | `units/days.yaml`, `years.yaml`, `dollars.yaml` |
| a response scale | `responses: satisfied5` | `scales/satisfied5.yaml`, `support4.yaml`, `support4_reversed.yaml` |
| a universe | `universe: all_respondents` | `universes/all_respondents.yaml`, `household_members.yaml` |
| an instruction | `instruction: select_one` | `instructions/select_one.yaml`, `select_all.yaml` |
| the missing-value codes | every question, without naming them | `missing.yaml` |

Concepts and units should always be shared: if you write one as words in a question,
QREtools offers to make it shared. The rest can also be written in the question itself.

QREtools points out repetition as you type:

- the same question text, response list, universe or instruction in two questions, or
  two shared scales with the same labels (a warning on each, with a link to the other);
- a response list, universe or instruction written out when a shared one already says
  it ("use the name");
- a question worded much like another (a note, quoting the other).

When two questions are alike on purpose, say so in either one, with how they differ,
and the warning goes away:

```yaml
variant_of:
  parks_spending: response-order experiment, options shown in reverse
```

## The example questions

`questions/example/` has one of each kind of question, to copy:

| Example | Shows |
|---|---|
| `consent.yaml` | one answer from a list written in the question, with a shared instruction |
| `service_satisfaction.yaml` | one answer from a shared scale, with a shared universe and instruction |
| `news_sources.yaml` | select all that apply, one variable per option |
| `library_visits.yaml` | a number with a range and a unit |
| `service_comments.yaml` | an open answer with a length limit, whose text includes an earlier answer (`fills:`) |
| `household_size.yaml`, `member_age.yaml` | a count, and a question asked once for each person counted |
| `parks_spending.yaml`, `parks_spending_reversed.yaml` | two questions alike on purpose (`variant_of`), on scales in opposite orders |

Name questions and folders however your team likes. QREtools reads nothing into either.

## Instruments

When you start your first instrument, QREtools offers "Open the example". It writes
`instruments/example.yaml`, which asks the example questions. It shows:

- asking for consent, and stopping if it's refused;
- sections;
- a computed value;
- a follow-up asked only of some people, whose text includes the label of an earlier answer;
- a check on an answer;
- a value from outside the interview;
- a split-ballot experiment on the two parks questions;
- a roster asking each household member's age.

Copy it or delete it. It reads this workspace as `../`, so point it at a release tag
before you merge it (below).

An instrument gives each source of questions a short name, then asks questions through
it (`ask: example.consent`). While you write it on your branch, its own workspace is
`../`, which reads the questions as they are now on your branch:

```yaml
uses:
  example: ../
```

Before it can be merged into `main`, it must read this workspace at a release tag, as it
would any other workspace:

```yaml
uses:
  example: your-org/this-workspace@v1
```

So questions are released before an instrument can use them:

- A new workspace has no release yet, so release `v1` first (see "Releasing this
  workspace").
- To use questions you've just added, merge them and release them first. Then point the
  instrument at the new tag, merge it, and release again. The instrument can't go in
  the same pull request as its new questions, because they need a tag first.

The check on every pull request refuses an instrument if it still reads `../`, if it
reads a tag that hasn't been recorded or has moved, or if anything in it is left to fill
in or fix.

Another workspace is named by its repository and a tag. The instrument reads that
workspace as it was at the tag, until you change the tag:

```yaml
uses:
  theirs: your-org/their-questions@v1  # owner/name@tag
```

Only tags are read, never branches, so that workspace's owners decide what to publish.
They release it with its Release workflow (below). Its questions keep the agency set in
its own `workspace.yaml`. If it's a private repository, the QREtools app must be
installed on it. Its questions open on GitHub; your own open in QREtools.

There are two version numbers:

- The tag (`v1`, `v2`) is the other workspace's version, chosen by whoever releases it.
- Each question's DDI version (the `2` in `urn:ddi:their.agency:a_question:2`) counts
  how many times that question has changed on the other workspace's `main`. So between
  two tags it can go up by more than one. A tag made by hand, without the Release
  workflow, carries no versions, so its questions read as version 1.

A tag can be moved to a different commit. So your workspace records the commit each tag
pointed to, under `locked:` in `workspace.yaml`. Saving an instrument writes these
lines; you don't edit them. If a tag later points somewhere else, the instrument shows a
warning. The next save records the new commit and puts `[WARNING]` in the commit
message, so whoever reviews the pull request knows the questions under that tag may have
changed.

From a terminal, `qretools instrument export` records the commit and prints the warning;
copy the `[WARNING]` line into your commit message. An instrument whose tag isn't
recorded (written in a text editor, say) can't be merged: the check on every pull
request refuses it. Saving it in QREtools records the lock. All this uses git alone, so
it works wherever your repositories are kept.

### Releasing this workspace

Other workspaces read this workspace at a release tag. A release tag is `v1`, `v2` and
so on.

Before the first release, give the Release workflow permission to push a tag:

1. In the repository, go to Settings → Actions → General.
2. Under "Workflow permissions", select "Read and write permissions".
3. Click "Save".

If you cannot select it, an owner of the organization must allow it.

**Caution: do not make, move or delete a tag by hand. A tag that you make by hand has
no DDI versions, so an instrument that reads it gives each question version 1. If you
move or delete a tag, the instruments that read it do not agree with their locks.**

To make a release:

1. On GitHub, go to Actions → Release.
2. Click "Run workflow".
3. Type the next tag, for example `v2`.
4. Click the green "Run workflow" button.

From a terminal, this `gh` command does the same:

```sh
gh workflow run release.yml -f tag=v2
```

The Release workflow puts the tag on the last commit on `main`. The message of the tag
gives the DDI version of each file. An instrument that reads the tag gets these versions.

If you did step 7 (Pages), the DDI workflow then publishes the release at
`https://<owner>.github.io/<name>/<tag>/`. The files of a release stay at that address.

A release includes the instruments of this workspace. Each instrument has one DDI
version. The version increases when a part of the instrument changes. It also increases
when a tag that the instrument reads changes. Between two releases, a version can
increase by more than one.

### Conditions

Conditions are written in VTL, the SDMX standard's expression language:
`example.service_satisfaction in {"4", "5"}`, `form = "1"`, `example.library_visits < 25`.
Codes are text, so put them in quotes. As you type, QREtools suggests questions after
`ask:`, names in conditions, and codes after `=`, and lists what's still to fill in or fix.

## Licence

This template is in the public domain under CC0 1.0 (`LICENSE`). Copy it, change it and
license your own workspace however you like, with no need to credit it. Replace this
section with your own licence once you've started.
