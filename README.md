# Wowool SDK Examples

This repository contains examples for the Wowool SDK.

Note: You need to request an evaluation license at philippe@wowool.com. We will happily provide one.

## Installation

First, install the dependencies:

    python3 -m venv .env && . .env/bin/activate
    pip install -r requirements.txt


## Running the examples

Now you can start running the samples. For example:

    python sdk/samples/english-entities.py

## Custom lexicons

See [the lexicon guide](rules/lexicons.md) for the syntax. The `.wow` samples are in `rules/`:

- `lexicon-inputs.wow`: matching on different input streams (literal, stem, normalized).
- `lexicon-wildcards.wow`: regular-expression wildcards in lexicon entries.
- `lexicon-canonicals.wow`: assigning canonicals to entries.
- `lexicon-canonical-match.wow`: matching on canonicals with `input="canonical"` (runs after `lexicon-canonicals.wow`).

Try one with the SDK, e.g. `Domain(source=Path("rules/lexicon-inputs.wow").read_text())`.

## Rules

See [the rules guide](rules/rules.md). The `.wow` samples in `rules/` are:

- `rule-tokens.wow`: literals, stems, part of speech, properties and regular expressions.
- `rule-operators.wow`: any token, repetition, shortest/longest match and unordered matching.
- `rule-annotations.wow`: annotations as elements, containers, anchors, attributes and negation (needs an `english,entity` pipeline).
- `match-*.wow`: samples that follow the documentation page on matching and capturing (literals, lemmas, part of speech, character classes, wildcards, compounds, canonicals, context rules, semantic rules and filtering). They are listed in the rules guide.
