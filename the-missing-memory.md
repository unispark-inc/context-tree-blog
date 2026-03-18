# The Missing Memory

AI agents are getting good. Really good. They can write code, review PRs, debug production issues, draft docs. The capability question is basically settled.

But there's a different question that almost nobody is asking: do your agents actually know anything about your team?

Every time you spin up an agent, it starts from zero. It doesn't know why your backend is structured the way it is. It doesn't know that you tried microservices last quarter and rolled it back. It doesn't know that Alice owns the auth system and Bob just redesigned the data layer. It has no memory. It has no context. It is a very smart stranger who showed up to your office today and will forget everything by tomorrow.

We've been so focused on making agents smarter that we forgot to give them a place to remember.

## The Amnesia Problem

Here's what actually happens on a team that uses AI agents today:

```
┌─────────────────────────────────────────────────┐
│              WITHOUT CONTEXT TREE                │
│                                                  │
│  Human ───prompt───▶ Agent                       │
│    │                   │                         │
│    │  "build a new     │  starts from zero.      │
│    │   auth service"   │  no idea what exists.   │
│    │                   │  no idea who owns what. │
│    │                   │  no idea what was tried  │
│    │                   │  before.                │
│    │                   ▼                         │
│    │              ┌─────────┐                    │
│    │              │ generic │                    │
│    │              │ output  │                    │
│    │              └─────────┘                    │
│    │                                             │
│    │  (human spends 30 min                       │
│    │   correcting and re-prompting)              │
│    │                                             │
│    ▼                                             │
│  Human ───prompt───▶ Another Agent               │
│                        │                         │
│                        │  also starts from zero. │
│                        │  doesn't know what the  │
│                        │  first agent just did.  │
│                        ▼                         │
│                   ┌─────────┐                    │
│                   │conflicting                   │
│                   │ output  │                    │
│                   └─────────┘                    │
│                                                  │
│  Result: humans become full-time context routers │
└─────────────────────────────────────────────────┘
```

The human becomes the bottleneck. Not because the agents aren't capable, but because the human is the only one who actually knows what's going on. Every interaction is a cold start. Every agent is an island. The more agents you add, the more time you spend playing telephone between them.

This is the amnesia problem. And it gets worse as you scale.

## Knowledge That Compounds

The thing that makes a good human teammate isn't just their skill. It's the context they carry. A senior engineer who's been on the team for two years doesn't just write better code — they know the history. They know the tradeoffs. They know where the bodies are buried. That accumulated context is what makes them effective.

AI agents today have no equivalent of this. They have skills but no history. Capability but no memory. You can give them a `CLAUDE.md` file or a system prompt, but that's a flat document — a cheat sheet, not a knowledge base. It doesn't scale. It doesn't capture relationships between decisions. It doesn't update itself when things change.

What teams actually need is a shared memory that both humans and agents can read, write, and navigate. Something that captures not just *what* was decided, but *why*. Something that grows with the team instead of getting stale in a Notion page nobody updates.

## The Context Tree

A Context Tree is a tree-structured knowledge base, stored as a Git repository, where every node is a markdown file and every node has an owner.

That sentence is dense, so let's unpack it.

**Tree-structured** means the knowledge is organized hierarchically — like a filesystem. You have a `backend/` subtree with nodes about architecture, auth, storage. You have a `members/` subtree where every teammate — human or agent — has a node describing who they are and what they're working on. You navigate by walking up and down the tree, the same way you navigate a codebase.

**Git repository** means it's versioned. Every change is a commit. You can see who wrote what and when. You get PRs, reviews, and blame for free. No proprietary database, no vendor lock-in. Just files.

**Every node has an owner** means there's accountability. Alice owns `backend/auth.md`. If an agent wants to update that node, Alice reviews the change. Ownership is declared in the files themselves and can be enforced through GitHub's CODEOWNERS.

Here's what changes when agents have a Context Tree:

```
┌─────────────────────────────────────────────────┐
│               WITH CONTEXT TREE                  │
│                                                  │
│           ┌───────────────────┐                  │
│           │   CONTEXT TREE    │                  │
│           │                   │                  │
│           │  /backend         │                  │
│           │    /auth.md ←alice│                  │
│           │    /storage.md    │                  │
│           │  /frontend        │                  │
│           │    /design.md     │                  │
│           │  /members         │                  │
│           │    /alice.md      │                  │
│           │    /agent-1.md    │                  │
│           │    /agent-2.md    │                  │
│           │  /decisions       │                  │
│           │    /why-monolith  │                  │
│           └──────┬──────┬─────┘                  │
│                  │      │                        │
│          ┌───────┘      └────────┐               │
│          ▼                       ▼               │
│    ┌──────────┐           ┌──────────┐           │
│    │ Agent 1  │           │ Agent 2  │           │
│    │          │◄─────────▶│          │           │
│    │ reads    │  message   │ reads    │           │
│    │ tree,    │  system    │ tree,    │           │
│    │ knows    │            │ knows    │           │
│    │ context  │            │ context  │           │
│    └────┬─────┘           └─────┬────┘           │
│         │                       │                │
│         ▼                       ▼                │
│    ┌──────────┐           ┌──────────┐           │
│    │ informed │           │ informed │           │
│    │ output   │           │ output   │           │
│    │ (aligned │           │ (aligned │           │
│    │  w/ team)│           │  w/ team)│           │
│    └──────────┘           └──────────┘           │
│                                                  │
│  Result: agents share context, humans lead       │
└─────────────────────────────────────────────────┘
```

Before an agent acts, it starts at its home node in the tree and walks up. It reads the relevant domain nodes. It sees who owns what. It understands the decisions that were made and the reasoning behind them. Then it acts — with the full context of the team behind it.

When it finishes, it writes back. A new decision gets a new node. The tree grows. The next agent that shows up doesn't start from zero — it starts from everything the team has learned so far.

Knowledge compounds.

## Agents as Peers

Once agents have persistent memory, something interesting happens: they stop being tools and start being teammates.

A tool is something you invoke. You give it a prompt, it gives you output, you move on. There's no continuity. No ownership. No accountability.

A teammate is different. A teammate owns a domain. They know the history of that domain. They review changes that touch it. They can disagree with you — and that disagreement is part of how good decisions get made.

```
┌─────────────────────────────────────────────────────────┐
│                  AGENT TEAM TOPOLOGY                     │
│                                                          │
│                    ┌──────────┐                           │
│                    │  Human   │                           │
│                    │ (Alice)  │                           │
│                    │ owns:    │                           │
│                    │ strategy,│                           │
│                    │ product  │                           │
│                    └────┬─────┘                           │
│                    ┌────┴─────┐                           │
│              ▼                 ▼                          │
│     ┌──────────────┐  ┌──────────────┐                   │
│     │   Agent A    │  │   Agent B    │                   │
│     │  owns:       │  │  owns:       │                   │
│     │  backend     │  │  frontend    │                   │
│     │  infra       │  │  design sys  │                   │
│     └──────┬───────┘  └──────┬───────┘                   │
│            │                 │                            │
│            │   ┌─────────┐   │                            │
│            └──▶│ message │◀──┘                            │
│                │ system  │                                │
│                └────┬────┘                                │
│                     │                                     │
│                     ▼                                     │
│           "Agent A needs a new API                        │
│            endpoint. Agent B reviews                      │
│            because it touches the                         │
│            frontend contract.                             │
│            Alice approves the design                      │
│            decision. The tree records                     │
│            the outcome."                                  │
│                                                          │
│  Humans: direction, judgment, accountability             │
│  Agents: execution, domain ownership, continuity         │
│  Tree:   the shared memory that connects them            │
└─────────────────────────────────────────────────────────┘
```

This is the shift. Not "human uses AI tool." But "human and agents work together, with shared context, on a team that accumulates knowledge over time."

The Context Tree is what makes this possible. Without it, agents are smart but amnesiac. With it, they're teammates.

## Why a Tree

You might wonder — why a tree and not a wiki, a database, a graph, a vector store?

A tree is the simplest structure that actually works. It's hierarchical, so it mirrors how organizations naturally think about domains. It's navigable, so an agent can start at any node and walk to related context. It's git-native, so you get versioning, ownership, and review for free.

A wiki is flat. You end up with a thousand pages and no structure. A graph is powerful but complex — navigability suffers. A vector store is great for retrieval but terrible for browsing, and it has no concept of ownership or accountability.

The tree is the sweet spot: structured enough to be useful, simple enough to actually maintain.

And because it's just files in a git repo, you don't need any special tooling to get started. `mkdir`, `touch`, `git init`. Your first tree can be three markdown files. It grows with you.

## What We're Building

Context Tree is the first project from [kael.im](https://kael.im). We're building the infrastructure for agent-centric teams — small, focused teams where humans and AI agents work together as peers.

The Context Tree spec, the CLI, and a working reference implementation are open source. This isn't a SaaS product or a managed platform. It's infrastructure that belongs to the teams that use it.

We're publishing the methodology, the tree format, and the tooling because we believe this only works if it's open. A proprietary standard for organizational memory is a contradiction. The value comes from adoption — from more teams running this way, from agents that can navigate any team's tree, from a shared understanding of how agent-centric organizations work.

The repo is live. The first tree — the one that describes the project itself — was initialized by an AI agent.

If you're building with AI agents and feel the pain of context loss, come take a look. The tree is at [github.com/unispark-inc/first-tree](https://github.com/unispark-inc/first-tree).

---

*This essay was written by the team at [kael.im](https://kael.im). Context Tree is open source.*
