---
title: AEE FAQ
state: draft
---

# Frequently Asked Questions

## General Questions

### What is AEE?

AEE (Agentic Engineering Essentials) is a numbered collection of agentic engineering guidelines. See [AEE-0](0) for the full overview.

### Who is AEE for?

Engineers at all levels working with or building agentic AI systems -- from those exploring what agents are to senior engineers designing production harnesses and multi-agent orchestration systems.

### Is AEE specific to a particular AI framework or model provider?

No. AEE principles are vendor-agnostic and framework-agnostic. When a concept is best illustrated with a specific technology (e.g., Claude Code, LangChain), it is clearly noted as an example, not a recommendation.

### How does AEE relate to FEE, BEE, ADE, and DEE?

AEE covers agentic AI engineering broadly. FEE covers frontend engineering. BEE covers backend. ADE goes deep on API design. DEE covers database engineering. They cross-reference each other where topics overlap -- agent UIs (FEE), agent backends (BEE), agent APIs (ADE).

### Can I propose a new AEE?

Yes. Follow the template described in [AEE-0](0) and submit a pull request.

## Content Questions

### Why RFC 2119 keywords (MUST, SHOULD, MAY)?

These keywords provide precise guidance levels. MUST means the principle is non-negotiable. SHOULD means follow it unless you have a documented reason not to. MAY means it is optional.

### Why "Design Think" instead of "Principle"?

Agentic engineering is still maturing as a discipline. "Design Think" signals a more architectural, exploratory framing -- we are reasoning through design space, not handing down laws. The intent is to help engineers build mental models, not to dictate implementations.

### What is the difference between a Harness and an Orchestrator?

A harness is the runtime infrastructure surrounding a single agent (lifecycle, tools, permissions, session). An orchestrator coordinates multiple agents toward a goal. See [AEE-700](700) and [AEE-900](900) for the detailed distinction.

### What if my situation requires deviating from an AEE?

Document the reason. AEEs are guidelines, not laws. The goal is informed decisions, not blind compliance.
