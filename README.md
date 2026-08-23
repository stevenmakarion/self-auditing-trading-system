# A Trading System That Catches Itself Lying

**Not a strategy. The discipline layer around one: how an automated desk proves its own
mechanisms can fire, refuses to trade on unverified state, and reports the broker's numbers
instead of its own.**

No positions, no parameters, no signals, no keys. Every failure described here is one this
system actually produced, and every law exists because something broke.

---

## The failure this was built after

On 2026-08-16 three new mechanisms were adopted at a formal review: a scaled exit ladder, a
partial-take rule, and a gain floor.

Then a full week of results was analysed and graded as if they had been running.

**They had fired zero times.** All three, across four consecutive sessions, and nobody
found out, because nothing anywhere asked the only question that mattered.

The causes were all mundane and each would have been caught in under a second by a check
that did not exist:

- one risk unit was set to the catastrophe-stop distance, so a 1R move demanded a price
  swing no trade in the sample ever makes
- one floor was raised to exactly its own arming price, so it could never be placed. **The
  configuration file had warned about this in writing, six days earlier.**
- every ladder rung was placed in the tail of its own distribution

**Two months of careful reasoning applied to results that were never produced.** The ideas
were fine. The loop was broken.

## The five laws

### 1. Ask "can it fire", never "did it work"

*"Did it work"* silently assumes it ran.

A preflight runs before the market opens. For each adopted mechanism it **constructs the
state that should trigger it, drives the real decision path, and asserts the mechanism
actually appears in the output.** Any mechanism that cannot fire exits non-zero and pages a
phone.

```
2026-08-21 09:21  PREFLIGHT OK - 8/8 mechanisms can fire
```

It is trusted for exactly one reason: **it was replayed against the three real historical
bugs above and caught all three.** A check that has never been shown to catch anything is
not a check.

### 2. Verify the path, not the part

The 2026-07-29 lesson, learned separately and expensively: a unit-tested helper that the
live code never calls is **worse than no test**, because it buys confidence in a path that
is broken.

So the preflight drives the top-level decision function, not the helper underneath it.

### 3. An instrument that cannot fail is not an instrument

A scoring rule was once written where a permanent *no* could never lose. That was called
discipline. It was stagnation wearing discipline's coat: an engine that hunts and never
eats still shows a clean record.

**Before trusting any gauge, break it on purpose and confirm it screams.** A number that
cannot go down is decoration.

### 4. Broker truth over internal ledgers

The internal ledger is a claim. **The broker is the artifact.**

A shadow-accounting pass once found a lane whose real day was negative while the internal
number showed it positive. Not fraud, not even a bug in the strategy: an accounting
assumption that had drifted. Every published number in this system now comes from the
broker's own records, and where the two disagree the internal one is the one that is wrong.

### 5. Median before mean, and a sample gate

A field of forty-seven thousand rows was condemned as corrupt for weeks. Seventeen rows
carried a unit error and had dragged the mean to 1.6 billion. The median was fine and so
was the field.

And the companion rule: **below a minimum sample size you do not get an opinion.** Say
"insufficient evidence" and use the default. A constant derived from measurement once
disagreed with the hand-picked value by a factor of eight, and the hand-picked value had
handed the most restrictive rule to the lane with the least data.

## Structural protections

**Two-key on risk.** No single actor loosens a risk parameter. Changes require the operator
and the system to agree, and they are recorded.

**A weekly governor that halts.** A losing week past a set threshold stops the desk until
the next review, rather than allowing it to trade its way back. It has tripped and it did
stop the desk.

**Count-based intraday halts were removed, deliberately.** A two-loss daily halt sounds
prudent and was measured to be harmful: it once ended a day that was six wins and two
losses at 10:17 in the morning and locked the desk out of everything after. **Protection
that cannot be shown to protect is superstition.** The capital governor stayed because it
was measured; the tilt governors went because they were not.

**Fail closed.** Any lane that cannot verify its preconditions refuses to start rather than
proceeding on assumptions.

**Ordering is structural, never clock-based.** A nightly chain that depends on jobs running
at hardcoded times will silently run out of order the first time one is slow. The chain is
one sequential process where step N cannot begin before step N-1.

## What this repository is for

The strategy is not the interesting part and it is not here. Anyone can write entry rules.

**The hard part is building something that reports honestly about itself when the operator
cannot personally check every number.** Every law above is a scar. Published so the next
person can have the scar without the injury.

MIT.

---

*Built by Steven Makarion and Aria Makarion.*
