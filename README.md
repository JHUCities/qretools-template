# A survey workspace for QREtools

Survey questions and their documentation, one YAML file per question, and the
instruments composed from them, one YAML file each. QREtools elaborates both to
DDI-Lifecycle 4.0.

What QREtools is and how it works: [About QREtools](https://github.com/JHUCities/qretools#readme).

This repository is the template for new workspaces:
<https://github.com/JHUCities/qretools-template>. Start one with
[Use this template](https://github.com/JHUCities/qretools-template/generate).
It holds example questions, in `questions/example`, and no instrument: when you start
your first instrument (New → Instrument…), QREtools offers to open an example beside
it, asking these questions.

## Set up a new workspace from this template

1. **Create your workspace:** "Use this template" → "Create a new repository". A private
   repository is fine.
2. **Install the QREtools app** on the new repository:
   <https://github.com/apps/qretools/installations/new> → choose your account or
   organisation → "Only select repositories" → this one. Without it you can read the
   workspace in QREtools but not save.
3. **Protect `main`:** Settings → Rules → Rulesets → New branch ruleset. Target the
   default branch; turn on "Require a pull request before merging", "Block force
   pushes" and "Restrict deletions". QREtools saves each author's work to their own
   branch (`qretools-<login>`); the workspace changes only when a pull request is merged.
4. **Tidy merged branches:** Settings → General → Pull Requests → "Automatically delete
   head branches".
5. **Set your agency:** in `workspace.yaml` (the gear beside the workspace's name in
   QREtools), replace `org.example` with the DDI agency your items are published under (letters, digits and
   hyphens, in parts joined by dots, such as `edu.example.survey-lab`). Without one,
   nothing can be exported as DDI.
6. **Open it** in QREtools: sign in at <https://qretools.com> and enter the repository as `owner/name`. The
   version that opens workspaces and instruments is in development and not published
   yet.

## From a terminal

`mise.toml` pins Node and QREtools' npm package, `qretools`, whose command checks a workspace. With
[mise](https://mise.jdx.dev): `mise install`, then `qretools check .` to list what's to
fill in or fix, as QREtools shows it, and `qretools export . -o ddi.json` for the DDI.
Without mise: `npx qretools@<the version in mise.toml> check .`. Change the version there
to upgrade.

`.github/workflows/check.yml` runs the same check on every pull request and push to `main`:
it fails on anything to fill in or fix, and annotates each error and warning on its line
in the pull request. To also fail on warnings, make its last line `qretools check . --strict`.

## Layout

| Path | Holds |
|---|---|
| `mise.toml` | the pinned tools: Node and the `qretools` CLI (see "From a terminal") |
| `workspace.yaml` | the workspace's settings: the DDI agency everything in it is published under (`agency:`), the fields every question must have (`required:`), more for one folder's questions (`required_in:`), and the commit each tag its instruments use is locked at (`locked:`) |
| `questions/<folder>/<name>.yaml` | one question each; the folder is chosen when a question is first saved, and says nothing about the question's name |
| `concepts/<name>.yaml` | shared concepts (`label:`, `definition:`), used by name from `concept:` |
| `units/<name>.yaml` | shared units (`label:`, `definition:`), used by name from `unit:` under `number:` |
| `scales/<name>.yaml` | shared response scales (`labels:`), used by name from `responses:` |
| `universes/<name>.yaml` | shared universes (`text:`), used by name from `universe:` |
| `instructions/<name>.yaml` | shared instructions (`text:`), used by name from `instruction:` |
| `missing.yaml` | the missing-value codes (`labels:`) |
| `select_all.yaml` | the two values a select-all-that-apply option is stored as (`chosen:`, `not_chosen:`); not here, so they are 1 Yes and 0 No |
| `instruments/<name>.yaml` | one instrument each (none yet: QREtools makes the first) |

Question names, and the names of shared files of one kind, are each used once in the
workspace, whatever folder a question is in. The codes in `missing.yaml` are this
template's example; use your own conventions.

`questions/example/` is the one folder whose name QREtools reads: its example
instrument asks these questions, and is offered only while they are saved there. Put
your own questions in folders of your own, and delete this one when you no longer want
the example. Its shared files (a scale, the universes, the units) stay, as they are
yours to use.

## Shared values, and keeping the workspace free of duplicates

Six things can be written once and named from any question:

| Shared | Named from | Example here |
|---|---|---|
| a concept (what is measured) | `concept: service_satisfaction` | `concepts/service_satisfaction.yaml` and three more |
| a unit | `number: { unit: days }` | `units/days.yaml`, `years.yaml`, `dollars.yaml` |
| a response scale | `responses: satisfied5` | `scales/satisfied5.yaml`, `support4.yaml`, `support4_reversed.yaml` |
| a universe | `universe: all_respondents` | `universes/all_respondents.yaml`, `household_members.yaml` |
| an instruction | `instruction: select_one` | `instructions/select_one.yaml`, `select_all.yaml` |
| the missing-value codes | every question, implicitly | `missing.yaml` |

Concepts and units are always shared: written as words in a question, QREtools offers
to make them shared. Anything else is written in the question.

QREtools points out repetition as you type:

- the same question text, response list, universe or instruction in two questions, or
  two shared scales with the same labels (a warning on each, linking to the other);
- a response list, universe or instruction written out that a shared one already says
  ("use the name");
- a question worded much like another (a note, quoting the other).

When two questions are alike on purpose, say so in either of them, with why they
differ, and the warning goes:

```yaml
variant_of:
  parks_spending: response-order experiment, options shown in reverse
```

## The example questions

`questions/example/` holds one of each kind of question, to copy:

| Example | Shows |
|---|---|
| `consent.yaml` | one answer from an inline list, with a shared instruction |
| `service_satisfaction.yaml` | one answer from a shared scale, with a shared universe and instruction |
| `news_sources.yaml` | select all that apply, one variable per option |
| `library_visits.yaml` | a number with a range and a unit |
| `service_comments.yaml` | an open answer with a length limit, its text filled in from an earlier answer (`fills:`) |
| `household_size.yaml`, `member_age.yaml` | a count, and a question asked once for each person counted |
| `parks_spending.yaml`, `parks_spending_reversed.yaml` | two questions alike on purpose (`variant_of`), on scales in opposite orders |

Name questions and folders however your team prefers: QREtools reads nothing into
either.

## Instruments

In QREtools, your first instrument offers "Open the example", which writes
`instruments/example.yaml`, asking these questions: consent and stopping on its answer,
sections, a computed value, a follow-up asked only of some respondents with its text
filled from the label of an earlier answer, a check on an answer, a value from outside
the interview, a split-ballot experiment on the two parks questions, and a roster asking
each household member's age. Copy it or delete it.

An instrument names where its questions come from under a short alias, then names the
questions with it (`ask: example.consent`). Its own workspace is always written `../`:

```yaml
uses:
  example: ../
```

Another workspace is named by its repository and a tag, so the instrument always reads
the same version of it until you change the tag:

```yaml
uses:
  theirs: your-org/their-questions@v1  # owner/name@tag
```

Only a tag is read, never a branch, so that workspace's owners decide what version is
published: they tag it. It is read with its own `workspace.yaml`, so its questions keep
the agency they were published under. A private repository needs the QREtools app
installed for QREtools to read it. Its questions open on GitHub; your own open in place.

A tag can be moved to another commit, so your workspace locks each tag its instruments
read to the commit it named, under `locked:` in `workspace.yaml`. Saving an instrument
writes those lines; you don't edit them. If a tag later names another commit, the
instrument shows a warning, and the next save locks it at the new commit with
`[WARNING]` in the commit message, so whoever reviews the pull request sees that the
questions under that tag may have changed. From the terminal, `qretools instrument
export` writes the lock and prints the warning; say so in your commit message. It relies on git alone, so it works wherever
your repositories are kept.

Conditions are written in VTL, the SDMX standard's expression language:
`example.service_satisfaction in {"4", "5"}`, `form = "1"`, `example.library_visits < 25`.
Codes are strings, so they are quoted. QREtools completes questions after `ask:`, names
in conditions and codes after `=`, and lists what is still to fill in or fix as you type.
