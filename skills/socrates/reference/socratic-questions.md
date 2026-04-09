# Socratic Question Types -- Detailed Guide

## 1. Clarification Questions

**Purpose:** Expose vagueness, ambiguity, and undefined terms.

**When to use:** The thesis uses terms that seem clear but could mean different things to different people, or the scope of a claim is not precisely bounded.

**Example questions:**
- "What exactly do you mean by [term]? Can you define it without using synonyms?"
- "Is this claim about all cases or specific cases? Where is the boundary?"
- "If two people read this thesis, would they understand the same thing?"

**What to look for:** Terms that everyone "knows" but nobody has defined. These are the most dangerous assumptions because they hide in plain sight.

**Engineering variant:** "When you say 'scalable', do you mean horizontal scaling, vertical scaling, or scaling of the team? At what threshold does the current approach fail?"

## 2. Assumption Probing Questions

**Purpose:** Surface premises treated as self-evidently true.

**When to use:** The thesis depends on something that is accepted without examination, or when the argument would collapse if a specific premise turned out to be false.

**Example questions:**
- "What would have to be true for this thesis to hold? Which of those things have we actually verified?"
- "What is the strongest version of the opposing argument? What assumptions does IT make?"
- "If we remove [assumption], does the conclusion survive?"

**What to look for:** Load-bearing assumptions -- the ones where, if they break, everything above them falls. Target these first.

**Engineering variant:** "This assumes the database will always be available. What happens under a network partition? This assumes linear growth -- what if growth is exponential?"

## 3. Evidence Probing Questions

**Purpose:** Test the strength of supporting evidence.

**When to use:** The thesis cites evidence, data, or precedent that hasn't been scrutinized. Or the thesis has no cited evidence at all.

**Example questions:**
- "What evidence supports this? Is it direct evidence or inferred?"
- "Could this evidence support a different conclusion equally well?"
- "What would count as evidence AGAINST this thesis?"

**What to look for:** Survivorship bias, confirmation bias, small sample sizes, and unfalsifiable claims. If nothing could disprove the thesis, it isn't saying anything meaningful.

**Engineering variant:** "The benchmark shows 10ms latency -- under what conditions? What's the p99? Were tests run under production-like load or synthetic load?"

## 4. Perspective Shifting Questions

**Purpose:** Reveal blind spots by adopting opposing viewpoints.

**When to use:** The thesis considers only one perspective, dismisses alternatives too quickly, or frames the problem in a way that might be arbitrary.

**Example questions:**
- "Who would disagree with this, and what is the strongest version of their argument?"
- "If we started from the opposite assumption, where would we end up?"
- "What does this problem look like from [stakeholder/timeframe/culture] that we haven't considered?"

**What to look for:** Framing effects -- the thesis may be "correct" only because the problem was framed in a way that makes it correct. Reframe the problem to see if the thesis survives.

**Engineering variant:** "This architecture optimizes for developer experience. What does it look like from the ops team's perspective? From the perspective of a developer joining the team in 2 years?"

## 5. Consequence Tracing Questions

**Purpose:** Follow the thesis to its logical conclusions and test them.

**When to use:** The thesis has not been followed to its endpoint, or its implications haven't been fully explored.

**Example questions:**
- "If this is true, what else must be true? Are those consequences acceptable?"
- "Taken to its extreme, where does this principle lead? Is there a point where it breaks down?"
- "What are the second-order effects? The third-order?"

**What to look for:** Reductio ad absurdum -- if following the thesis leads to absurd or contradictory conclusions, the thesis contains a flaw. Also look for unintended consequences that the thesis ignores.

**Engineering variant:** "If we optimize for this metric, what happens to other metrics? If every team adopted this pattern, would the system still work? What happens when this scales 100x?"

## Choosing the Right Question Type

```
Start with the thesis. Ask:

1. Are the TERMS clear?
   No -> Clarification
   Yes -> continue

2. Are there UNSTATED PREMISES?
   Yes -> Assumption Probing
   No -> continue

3. Is the EVIDENCE strong?
   Weak/missing -> Evidence Probing
   Strong -> continue

4. Is there only ONE PERSPECTIVE?
   Yes -> Perspective Shifting
   No -> continue

5. Have CONSEQUENCES been traced?
   No -> Consequence Tracing
   Yes -> The thesis is well-examined (rare)
```

When in doubt, use Assumption Probing -- it is the most universally applicable and most likely to produce mutation.
