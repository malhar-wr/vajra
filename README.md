# Vajra⚡️ — Aha! Moment For Agents

> **Context engineering at the speed of a thunderbolt.**

Lightning doesn't search the whole sky. Neither should your agent.

Vajra is a small, open **context layer** for workforce questions. It turns public job descriptions and synthetic employee profiles into a skills graph, then serves an agent **one cited path through that graph** instead of the whole haystack. Same model, same question — a fraction of the tokens, and every fact with its receipt.

> **Status:** proposal. Architecture is being finalised. Nothing here is production software; it's a playground to learn from, fork and improve.

---

## Why this exists

Imagine joining a team as a context engineer and getting **one day** to understand context graphs and context layers for agents and humans. Vajra is the repo you'd want on that day: clone it, run it, poke at it, and come away clear — and curious.

Most "give the agent context" approaches dump raw documents into the context window and hope. Context fails in predictable ways when you do that: it gets poisoned, distracted, confused and contradictory. Vajra shows the alternative, end to end, small enough to read in an afternoon.

## The aha moment

Same question. Same model. Two answers, side by side:

| | Raw dump | Context served by Vajra |
|---|---|---|
| What the agent gets | All JDs and profiles | One struck path + evidence |
| Tokens | tens of thousands | ≤ 1,008 |
| Answer | confident, partly wrong | correct, every claim cited |

Underneath sits the **strike view**, so a human can see *why* those tokens were chosen.

## The lightning model

| Lightning | Context retrieval |
|---|---|
| Charge stored in the cloud | Knowledge stored in the graph |
| Potential difference | The question's need |
| Stepped leaders branching out | Cheap exploration of many graph paths |
| Return stroke | The one path committed to the context window |
| Repeat strokes | Cached, reinforced paths |

Where the analogy breaks is the lesson: lightning takes the path of least resistance, which in a graph means **hub nodes** like "Communication" that connect to everything and say nothing. Vajra penalises hubs on purpose. Don't strike the tallest boring tree.

A quick vocabulary, because it trips everyone up:

- **Taxonomy:** a controlled vocabulary arranged as "is a kind of". It is acyclic, and a node can have more than one parent.
- **Ontology:** the schema of typed relationships (person *has skill*, role *requires* skill, skill *supports* task). Think `CREATE TABLE`.
- **Knowledge graph:** the actual data following that schema. Think the rows.

## Scope

**Vajra is a context layer, not an agent.**

In scope:

- ingesting public data into the stores
- the skills graph and its evidence
- strike retrieval with a hub penalty
- a token-budgeted, cited **context package** that any client can request

Out of scope:

- agent loops, planners, prompt tuning
- authentication, multi-tenancy, connectors to HR systems
- any UI beyond the strike view
- any proprietary or company data

Claude Code (or Claude Cowork) is used only as the **demo client** that proves the layer works.

## Data (public only)

| Source | Used for | Licence |
|---|---|---|
| [ESCO](https://esco.ec.europa.eu/) | Skills taxonomy | Free reuse with attribution (European Commission) |
| [O*NET](https://www.onetcenter.org/) | Tasks, occupations, alternate job titles | CC BY 4.0 |
| Public job descriptions | 21 roles | Source URL kept with every JD |
| Synthetic employees | 108 profiles | Generated, no real people |

Every fact in Vajra points back to the document it came from. A project about provenance has to practise it.

## Stack: three embedded stores, zero servers

| Store | Role |
|---|---|
| **SQLite** | Master and transactional data: entities and an append-only evidence log |
| **[Relate](https://relatedb.ai)** | The context graph, queried with Cypher (embedded, single file, Apache-2.0) |
| **DuckDB** | The analytical lens: trends and aggregates over the same data |

The point: a context layer is not one database. Master, contextual and analytical concerns each live where they fit.

**Open architecture decisions** (next session):

1. Write path: does data land in SQLite first and get projected into the graph, or go straight into the graph?
2. Client contract: what does a client call, and what shape is the context package it gets back?

## The numbers

| | |
|---|---|
| Stores | 3 |
| Public job descriptions | 21 |
| Synthetic employees | 108 |
| Gold workforce questions | 21 |
| Pass bar | ≥ 18 of 21 correct |
| Max hops per strike | 9 |
| Context budget | ≤ 1,008 tokens |
| Answers the hub penalty must flip | ≥ 3 |
| Uncited claims allowed | 0 |
| Fresh clone to running | < 3 minutes |

## Goal (SMART)

- **Specific:** Build a public context layer that loads 21 public JDs and 108 synthetic employees, mapped to ESCO skills and O*NET tasks, across 3 embedded stores. For any workforce question it serves a cited context package, demonstrated through Claude Code as the client.
- **Measurable:** 18 or more of 21 gold questions answered correctly. 0 uncited claims. Context package of 1,008 tokens or less, compared against a raw-dump baseline in the same client. The hub penalty flips at least 3 answers. Clone to running in under 3 minutes.
- **Action-biased:** Each stage of the repo delivers one aha of its own (see below).
- **Reality-grounded:** Open data and embedded stores only. The only genuinely new ideas are the strike view and hub scoring; everything else is proven.
- **Time-bound:** Built over one weekend, Saturday 9:00 to Sunday 18:00 IST. A newcomer should go from clone to "I get it" in one day or less.

## Stages (each one is its own aha)

1. **Land** — public JDs and synthetic profiles become entities and evidence in SQLite.
2. **Connect** — the skills graph forms in Relate, mapped to ESCO and O*NET IDs.
3. **Strike** — a question explores many branches, penalises hubs, and commits one path.
4. **Serve** — raw dump vs. Vajra context package, side by side in the same client.
5. **See trends** — DuckDB answers the aggregate questions a graph shouldn't.

## Measuring tokens honestly

Claude Code adds its own overhead to every request (system prompt, tool definitions, skills). So Vajra reports the **difference** between the two arms run through the identical client, not the raw totals.

A small script also calls the API directly and reads the usage fields, so anyone can reproduce the numbers.

## Example gold questions

- Who among our 108 employees could move into a Data Engineer role, and what is each person missing?
- Which skills do the most roles require, excluding generic hubs?
- Which tasks in the Analyst roles depend on skills nobody currently has?

*(Full set of 21 to be written with known answers before any retrieval code.)*

## Inspiration

- Andrej Karpathy's small, scoped concept projects, which turn one idea into something you can poke at.
- The context engineering writing of Anthropic, Drew Breunig, Lance Martin and others.
- Zep/Graphiti's work on temporal, provenance-carrying knowledge graphs.

## Licence

To be decided (likely MIT or Apache-2.0). Data sources keep their own licences and attribution.
