# A survey workspace for QREtools

Survey questions and their documentation, one YAML file per question, kept in a question
bank, and the instruments composed from them, one YAML file each. QREtools elaborates
both to DDI-Lifecycle 4.0.

What QREtools is and how it works: [About QREtools](https://github.com/JHUCities/qretools#readme).

This repository is the template for new workspaces:
<https://github.com/JHUCities/qretools-template>. Start one with
[Use this template](https://github.com/JHUCities/qretools-template/generate).
It holds one bank of example questions, `banks/example`, and no instrument: when you
start your first instrument (New → Instrument…), QREtools offers to open an example
beside it, asking these questions.

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
5. **Set your agency:** in `workspace.yaml` and `banks/example/bank.yaml`, replace
   `org.example` with the DDI agency your items are published under (letters, digits and
   hyphens, in parts joined by dots, such as `edu.example.survey-lab`). Without one,
   nothing can be exported as DDI.
6. **Open it** in QREtools: sign in and enter the repository as `owner/name`. The
   version that opens workspaces and instruments is in development and not published
   yet.

## Layout

| Path | Holds |
|---|---|
| `workspace.yaml` | what the workspace says about itself: the DDI agency its instruments are published under (`agency:`) |
| `instruments/<name>.yaml` | one instrument each (none yet: QREtools makes the first) |
| `banks/example/` | the example question bank, laid out as below. QREtools' example instrument asks its questions, and is offered only while this bank is here under this name with these questions: add your own bank beside it (New → Bank…), and delete this one when you no longer want the example |

A bank's own layout:

| Path | Holds |
|---|---|
| `questions/<folder>/<name>.yaml` | one question each; the folder is chosen when a question is first saved, and says nothing about the question's name |
| `concepts/<name>.yaml` | shared concepts (`label:`, `definition:`), used by name from `concept:` |
| `units/<name>.yaml` | shared units (`label:`, `definition:`), used by name from `unit:` under `number:` |
| `scales/<name>.yaml` | shared response scales (`labels:`), used by name from `responses:` |
| `universes/<name>.yaml` | shared universes (`text:`), used by name from `universe:` |
| `instructions/<name>.yaml` | shared instructions (`text:`), used by name from `instruction:` |
| `missing.yaml` | the bank's missing-value codes (`labels:`) |
| `bank.yaml` | what the bank says about itself: the DDI agency its items are published under (`agency:`) |

`scales/yesno01.yaml` must stay as it is (`0: No`, `1: Yes`): select-all-that-apply
questions record each option on it. The codes in `missing.yaml` are this template's
example; use your own conventions. Instruments and banks are kept apart: never put an
instrument inside a bank's folders.

## Shared values, and keeping the bank free of duplicates

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

`banks/example/questions/` holds one of each kind of question, to copy:

| Example | Shows |
|---|---|
| `interview/consent.yaml` | one answer from an inline list, with a shared instruction |
| `examples/service_satisfaction.yaml` | one answer from a shared scale, with a shared universe and instruction |
| `examples/news_sources.yaml` | select all that apply, one variable per option (coded on `yesno01`) |
| `examples/library_visits.yaml` | a number with a range and a unit |
| `examples/service_comments.yaml` | an open answer with a length limit, its text filled in from an earlier answer (`fills:`) |
| `examples/household_size.yaml`, `member_age.yaml` | a count, and a question asked once for each person counted |
| `examples/parks_spending.yaml`, `parks_spending_reversed.yaml` | two questions alike on purpose (`variant_of`), on scales in opposite orders |

Name questions and folders however your team prefers: QREtools reads nothing into
either.

## Instruments

In QREtools, your first instrument offers "Open the example", which writes
`instruments/example.yaml`, asking these questions: consent and stopping on its answer,
sections, a computed value, a follow-up asked only of some respondents with its text
filled from the label of an earlier answer, a check on an answer, a value from outside
the interview, a split-ballot experiment on the two parks questions, and a roster asking
each household member's age. Copy it or delete it.

An instrument names each bank it uses under a short alias, then names the bank's
questions with it (`ask: bank.consent`). A bank kept in this repository is named by
where it is from the instrument's own folder:

```yaml
uses:
  bank: ../banks/example
```

A bank in a repository of its own is named by its repository and a tag, so the
instrument always reads the same version of it until you change the tag:

```yaml
uses:
  theirs: your-org/their-bank@v1            # owner/name@tag
  shared: your-org/workspace/banks/main@v2  # owner/name/folder@tag, for a bank in a folder
```

Only a tag is read, never a branch, so the bank's owners decide what version is
published: they tag it. A private bank's repository needs the QREtools app installed
for QREtools to read it. Its questions open on GitHub; a bank kept here opens in place.

Conditions are written in VTL, the SDMX standard's expression language:
`bank.service_satisfaction in {"4", "5"}`, `form = "1"`, `bank.library_visits < 25`.
Codes are strings, so they are quoted. QREtools completes questions after `ask:`, names
in conditions and codes after `=`, and lists what is still to fill in or fix as you type.
