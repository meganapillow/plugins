# kerryback-tutors

A Claude Code marketplace of tutoring plugins. Each one coaches a student
through a piece of finance or investments material one-on-one — the student
produces the numbers and Claude diagnoses the wrong answers, rather than
presenting worked solutions.

## Installing

Add the marketplace once:

```
claude plugin marketplace add kerryback/tutors
```

Then install whichever tutors you want:

```
claude plugin install binomial-tutor@kerryback-tutors
```

Both work inside a running session too, as `/plugin marketplace add …` and
`/plugin install …`.

## What's here

| Plugin | What it teaches |
|---|---|
| [binomial-tutor](plugins/binomial-tutor) | Binomial option pricing: replicating portfolios and no arbitrage, risk-neutral probabilities, multi-period backward induction, American options and early exercise |

## Layout

```
.claude-plugin/marketplace.json      the marketplace manifest
plugins/<name>/
├── .claude-plugin/plugin.json       the plugin's own manifest and version
├── README.md
└── skills/<name>/SKILL.md           what Claude actually reads
```

A plugin's version lives in `plugins/<name>/.claude-plugin/plugin.json`. The
marketplace manifest deliberately does not repeat it, so there is only one copy
of the number.
