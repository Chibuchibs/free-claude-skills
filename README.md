# Two free Claude skills

Two skills for [Claude Code](https://claude.com/claude-code), free and complete.
Both came out of real bugs on shipped products, and the numbers in them were
measured rather than estimated.

| Skill | What it does |
|---|---|
| [`randomness-correctness`](skills/randomness-correctness/SKILL.md) | Catch shuffles and odds that aren't as fair as they look |
| [`idea-kill-search`](skills/idea-kill-search/SKILL.md) | Kill a bad business idea in an hour instead of three months |

## Install

```bash
git clone https://github.com/Chibuchibs/free-claude-skills.git
mkdir -p ~/.claude/skills
cp -r free-claude-skills/skills/* ~/.claude/skills/
```

Claude picks them up next session and decides when to use them. Drop them in
`.claude/skills/` instead if you only want them on one project. They're plain
Markdown, so they also work in the Claude desktop and web apps, or pasted into
any assistant that takes custom instructions.

---

## Why `randomness-correctness` exists

This line is in a lot of codebases:

```js
const shuffled = items.sort(() => Math.random() - 0.5);
```

It is not a shuffle. Sort comparators are expected to be consistent, and this
one isn't, so the engine produces skewed permutations.

Here's where the correct answer landed across 200,000 runs of a four-option
quiz:

| Position | Should be 25% | Actual |
|---|---|---|
| 0 | 25% | **28.3%** |
| 1 | 25% | **28.2%** |
| 2 | 25% | 24.7% |
| 3 | 25% | **18.8%** |

Always guessing the first option beat guessing at random. Anyone who noticed had
an edge, and the quiz was quietly unfair to everyone who didn't.

Check it yourself:

```bash
node -e "
const pos=[0,0,0,0], N=200000;
for(let i=0;i<N;i++){
  const o=['a','b','c','d'].map((t,i)=>[t,i]).sort(()=>Math.random()-0.5);
  pos[o.findIndex(x=>x[1]===0)]++;
}
pos.forEach((c,i)=>console.log(i, (c/N*100).toFixed(1)+'%'));"
```

The skill has the Fisher–Yates fix, how to test a distribution properly, the
trap that makes your *test* wrong rather than your code, and how to check that
any odds you display to users match what the code actually rolls.

One thing worth knowing if you go looking for this bug: it usually appears more
than once. In the codebase it came from, it was in **eleven** places.

## Why `idea-kill-search` exists

Three searches, run before building anything:

```
<idea> competitors pricing
<idea> free alternative
<idea> regulation <current year>
```

The third is the one people skip, and it kills ideas the other two never would.
A rule change can make a working business unbuildable overnight and leaves no
competitors behind to warn you, because they all quit.

A real example from the skill: a payments reconciliation tool looked viable on
the first two searches — a few competitors, all enterprise-priced. The third
search found that the central bank had introduced a rule removing the data
access the product depended on. Dead, in an hour, instead of three months in.

The skill also has four structural checks that catch what searching misses:
who owns the pipe and would they ship it themselves, whether the hard part is
actually the code, what the real bottleneck is, and what you already have live
but unmarketed.

---

## These are two of sixteen

The other fourteen cover deployment, hosting, verification, retention and
marketing — things like testing whether your on-screen instructions actually
work, pricing a sponsored post by reach rather than member count, and moving
hosts without breaking your email.

[Ship & Sell — the full pack](https://solofounderskills.com/)

If these two are useful, those are the same thing. If they're not, don't buy it.

## Licence

MIT. Use them, change them, build on them.
