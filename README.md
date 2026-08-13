# Rice Business plugins for Claude Code

The `ricebusiness` marketplace. It currently ships one plugin, `tutor`: a
shared collection of tutoring skills that coach a student through a piece of
course material one-on-one — the student produces the answers and Claude
diagnoses the wrong ones, rather than presenting worked solutions.

## Installing

```
claude plugin marketplace add ricebusiness/plugins
claude plugin install tutor@ricebusiness
```

Both work inside a running session too, as `/plugin marketplace add …` and
`/plugin install …`.

One install brings every tutor in the collection, and new ones arrive when you
update.

## The tutors

| Command | Subject | Contributed by |
|---|---|---|
| `/tutor:binomial` | Binomial option pricing: replicating portfolios and no arbitrage, risk-neutral probabilities, multi-period backward induction, American options and early exercise | Kerry Back |
| `/tutor:agency` | Agency and authority in business law: actual authority, apparent authority, ratification and estoppel, and who is liable on the contract | Kerry Back |

Students can also just say what they want — "teach me the binomial model",
"when is a company bound by what its manager signed" — and Claude picks the
right tutor.

## Contributing a tutor

Add a folder under [plugins/tutor/skills/](plugins/tutor/skills). It needs a
`SKILL.md` with `name` and `description` in the frontmatter; reference files
and scripts are optional. The folder name becomes the command, so
`skills/capm/` is `/tutor:capm`.

Then add a row to the table above and to the one in the
[plugin README](plugins/tutor/README.md), and bump the version in
`plugins/tutor/.claude-plugin/plugin.json`.

That is the whole process. There is no second plugin to create and no
marketplace entry to add, and everyone who has already installed `tutor` gets
your tutor on their next update.

Attribution lives in the contributed-by column, not in the name. A tutor is
named for its subject so the command stays right when someone else takes it
over or extends it.

## Layout

```
.claude-plugin/marketplace.json      the marketplace manifest
plugins/tutor/
├── .claude-plugin/plugin.json       the plugin's manifest and version
├── README.md
└── skills/<subject>/SKILL.md        what Claude actually reads
```

Everything ships as one plugin, `tutor`, because the plugin name is the command
namespace: a skill in a plugin is invoked as `/<plugin>:<skill>`. One plugin
holding many skills gives `/tutor:binomial` and `/tutor:agency`, where a plugin
per subject would give `/binomial:binomial`.

The version lives in `plugins/tutor/.claude-plugin/plugin.json`. The marketplace
manifest deliberately does not repeat it, so there is only one copy of the
number.
