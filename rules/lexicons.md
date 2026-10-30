# Wowool lexicons

A lexicon maps words or phrases to a concept (annotation) and can attach
attributes. Syntax:

```wowool
lexicon: (input="...") {
    entry one,
    entry two : canonical form,
} = Concept@(attribute="value");
```

Entries are separated by commas, and a space inside an entry is significant.
Concept names must be longer than one character. Load a `.wow` file as a `Domain` on top of
a pipeline: `Domain(source=Path("file.wow").read_text())(pipeline(text))`.

## Input streams

By default an entry matches the literal text. Choose another stream with
`(input="...")`:

| input | matches |
|-------|---------|
| *(default)* literal | the word as written |
| `stem` | the dictionary form (`kiwis` → `kiwi`), case sensitive |
| `normalized_literal` | the literal, ignoring case and accents (`Agnès` = `AGNES`) |
| `normalized_stem` | the stem, ignoring case and accents |
| `component` / `head` | the word or last part of a compound (compounding languages) |
| `canonical` | the canonical set by an earlier domain or lexicon |

Sample: [lexicon-inputs.wow](lexicon-inputs.wow)

## Wildcards

Entries accept regular-expression operators. Repetition and alternation must be
applied to a parenthesized group:

| operator | example | matches |
|----------|---------|---------|
| `(.)+` / `(.)*` | `(.)+straat`, `house(.)*` | one or more / zero or more characters |
| `(x)?` | `colour(s)?` | optional |
| `(a\|b)` | `(colon\|small intestine) cancer` | alternatives |
| `(x)+` | `(ha)+` | repetition |

A space is a regular character: write `single (white )?female` so the optional
part includes its space.

Sample: [lexicon-wildcards.wow](lexicon-wildcards.wow)

## Canonicals

Assign a canonical with `entry : canonical`, or with a `canonical` attribute.
All variants then share the same canonical form:

```wowool
lexicon: (input="normalized_literal") {
    IBM : International Business Machines,
    Big Blue : International Business Machines,
} = TechCompany@(sector="it");

lexicon: { NYC } = Town@(canonical="New York City");
```

A later domain (a separate file, since the canonical must already be set) can match on the canonical instead of repeating every variant:

```wowool
lexicon: (input="canonical") { International Business Machines } = Employer;
```

Sample: [lexicon-canonicals.wow](lexicon-canonicals.wow), [lexicon-canonical-match.wow](lexicon-canonical-match.wow)

More: [Wowool documentation](https://www.wowool.com/docs).
