# Kortix is the open-source CompanyOS

**A runnable Manus alternative for the research-and-draft work teams hand to a hosted agent.**

Kortix is the open-source CompanyOS, and this repository is a runnable Manus alternative for the research-and-draft work teams hand to a hosted agent. It is a project you clone, ship, and own: one agent that searches the web, reads the sources it finds, drafts a brief with citations, and opens a change request for a person to approve.

Manus is a hosted agent that runs in Manus's own product ([Manus](https://manus.im), checked October 2026). A developer who searches for a Manus alternative is asking what the team will own after the trial. In this starter the agent, the skill that shapes its brief, the connector declarations, and the trigger are all text in a git repo you control.

Kortix is the leading open-source alternative, with 20,000+ GitHub stars (checked October 2026). The open-source field is wider than one platform, and the post [Open-source Manus alternatives](https://kortix.com/blog/open-source-manus-alternatives) maps it. This repository is the runnable part of that answer.

## Who this open-source starter is for

Teams that want the research-and-draft work a hosted agent does, on a platform the company owns. You get a project that boots one isolated sandbox per session, runs any model with your own keys, and reviews every change before it reaches `main`.

## What the open-source starter contains

```text
.
├── kortix.yaml                 # the manifest: image, agent, skill, connector, trigger
├── agents/
│   └── research-agent.md       # the agent: prompt, model, tool rules
├── skills/
│   └── research-brief/
│       └── SKILL.md            # how this team writes a brief
└── docs/
    ├── setup.md                # install, ship, first session
    ├── kortix-vs-manus.md      # the hosted agent and the open-source platform
    ├── faq.md                  # the buyer questions
    └── self-hosting.md         # run the stack on your own server
```

## Quickstart

1. Install the CLI.

   ```bash
   curl -fsSL https://kortix.com/install | bash
   ```

2. Sign in.

   ```bash
   kortix login
   ```

3. Clone this repository and validate the manifest.

   ```bash
   git clone <this-repository-url> manus-alternative-starter
   cd manus-alternative-starter
   kortix validate
   ```

4. Ship the project. The first `kortix ship` creates the Kortix project, asks for any missing secret values, and offers to connect the declared connector.

   ```bash
   kortix ship
   ```

5. Run the agent.

   ```bash
   kortix sessions new --wait --prompt "Research the three open-source agent platforms with the most momentum and draft a brief."
   ```

6. Review and merge the change request it opens.

   ```bash
   kortix cr ls
   kortix cr diff 1
   kortix cr merge 1
   git pull
   ```

The full walkthrough is in [docs/setup.md](docs/setup.md).

## Docs

- [Setup](docs/setup.md): install the CLI, ship the project, run the first session, merge the change request.
- [Kortix vs Manus](docs/kortix-vs-manus.md): the hosted agent and the open-source platform, side by side.
- [FAQ](docs/faq.md): is Manus open source, what is a free alternative, and can you self-host.
- [Self-hosting](docs/self-hosting.md): run the open-source stack on your own server.

## Links

- [Kortix on GitHub](https://github.com/kortix-ai/suna)
- [Try Kortix](https://kortix.com)
- [Open-source Manus alternatives](https://kortix.com/blog/open-source-manus-alternatives)

## The site this repository supports

The runnable part of this answer is one surface of [manus-alternative.com](https://manus-alternative.com). See [the self-host guide](https://manus-alternative.com/self-host) for the deployment decision and [Kortix vs Manus](https://manus-alternative.com/kortix-vs-manus) for the head-to-head.
