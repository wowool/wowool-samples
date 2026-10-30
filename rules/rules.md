# Wowool rules

Rules are more expressive than lexicons: they match tokens (literal, stem, part
of speech, properties) and other annotations, and they can address context,
containers and attributes. Use lexicons for word lists, rules for patterns.

```wowool
rule: {            // capture group: the part that becomes the annotation
    ...            // linguistic pattern
} = Concept@(attribute="value");
```

Every rule needs a capture group. Capture groups can be nested, and text outside
the capture group only acts as context. Load a `.wow` file like a lexicon:
`Domain(source=Path("file.wow").read_text())(pipeline(text))`. The samples that
use annotations such as `Person` or `City` need an `english,entity` pipeline.

## Tokens

| element | meaning | example |
|---------|---------|---------|
| `"literal"` | the word as written | `"Hello"` |
| `'stem'` | the dictionary form | `'leave'` matches *left*, *leaving*, *leaves* |
| `Pos` | part of speech (`Adj`, `Adv`, `Det`, `Nn`, `Num`, `Prep`, `Pron`, `Prop`, `V`, ...) | `Adj` |
| `+property` | token property (`+init-cap`, `+all-cap`, `+currency`, ...) | `+all-cap` |
| `<a,b>` | several dimensions on one token | `<"left",Adj>` |
| `<length(min,max)>` | length filter | `<length(20,),"([a-z])+">` |

Regular expressions work inside quoted literals and stems, for example
`'(.)*wife'`. A hyphen is a token of its own, so `3-year-old` is five tokens.

Sample: [rule-tokens.wow](rule-tokens.wow)

## Operators

| operator | meaning |
|----------|---------|
| `<>` | any token |
| `(x)?` `(x)*` `(x)+` `(x){n,m}` | token repetition |
| `(a \| b)` | alternatives |
| `..` | shortest match: the first following element |
| `.?` | shortest span: the closest pair, with no other candidate in between |
| `...` | longest match |
| `%%` / `%` | unordered shortest match / unordered adjacent |

Repetitions are greedy, so `'kill' (<>){0,3} Nn` stretches as far as it can
before it needs a noun. Use `..` when you want the closest element. `%%` and `%`
need parentheses around the whole expression. A capture group cannot start or
end with a shortest-match operator.

Sample: [rule-operators.wow](rule-operators.wow)

## Annotations

Annotations from lexicons, other rules and earlier domains (such as `Person`,
`City`, `Company` from the entity domain) are elements like tokens:

| pattern | meaning |
|---------|---------|
| `Person 'visit' (City \| Person)` | use annotations as elements |
| `Person[ Celebrity ]` | match the content of an annotation (container) |
| `Company[ ^ <> ^ ]` | `^` anchors content to the start or end of the container |
| `City@(country="Spain")` | condition on an attribute |
| `Person@(position=f"{rule.Position.literal()}")` | formatted attribute built from the match |
| `Sentence[ ... ]` | the whole sentence as a container |
| `!( 'car' )` | negative lookahead; the token still has to be consumed with `<>` |

Sample: [rule-annotations.wow](rule-annotations.wow)

## Matching and capturing

These samples follow the [Matching & capturing](https://www.wowool.com/docs/nlp/matching-&-capturing)
page of the documentation. Each file has its sample text and expected matches
in comments.

| topic | sample | notes |
|-------|--------|-------|
| literals and lemmas | [match-literals-and-lemmas.wow](match-literals-and-lemmas.wow) | `input="lemma"`, `input="normalized_lemma"`, `'lemma'` in rules |
| part of speech | [match-part-of-speech.wow](match-part-of-speech.wow) | `( Nn \| Adj )+ Nn`, `<'work',V>` |
| character classes | [match-character-classes.wow](match-character-classes.wow) | `[:upper:]`, `[:digit:]`, `[:xdigit:]`, `[:alnum:]`, `[:range(a-b):]`, `+nf` |
| wildcards | [match-wildcards.wow](match-wildcards.wow) | `(.)*soft`, `"(.)*ing"` |
| compounds | [match-compounds.wow](match-compounds.wow) | `input="head"`, `input="component"`, `h'...'`, `c'...'` (Dutch pipeline) |
| canonicals of entities | [match-canonical-entities.wow](match-canonical-entities.wow) | follows names and pronouns of an entity |
| context rules | [match-context.wow](match-context.wow) | annotate unknown names from the surrounding words |
| semantic rules | [match-semantics.wow](match-semantics.wow) | lexicons, rules and attributes combined |
| filtering | [match-filter.wow](match-filter.wow) | `wow::filter@(concept="Person")` |

Most samples run on `english,entity`; the compound sample needs `dutch`.

## Notes

- Each rule in the samples lists its own sample text and the expected matches in
  the comments above it.
- Concept names must be longer than one character.
- Three forms from the documentation do not compile with the current SDK: a bare
  regular expression in a token (use `"([a-z])+"`), `{ ... }` to open a
  capture group (use `{ (<>)+ }`), and `wow::filter@(entity=...)` (use `concept=`).
- Rules can only see annotations that already exist, so a rule that uses
  `Person` needs the entity domain to run first.

More: [Wowool documentation](https://www.wowool.com/docs), and see
[lexicons.md](lexicons.md) for lexicons.
