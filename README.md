# Reducing Spatial Hallucination in LLMs Through External Memory Architecture

## The Problem

Large language models hallucinate spatial facts. In navigable environments, they will confabulate paths, move through walls, and lose track of where they are in the environment. This is simply a consequence of the attention architecture. LLMs predict the next token from learned patterns and ongoing context but when the context grows too large, some of it is trimmed or summarized and later inference builds on these incorrect assumptions. When asked a spatial question, they generate whatever sequence of words is most statistically plausible given everything that came before. This is of course, a more universal problem, though here we are concerned with a narrower domain. 

Anyways, this makes LLMs unusable as autonomous agents in any setting that requires spatial consistency like for robots in 3D space or even in video games as video game NPCs.

## The Proposal

The idea is to remove spatial memory from the LLM entirely. The model never remembers where it has been, what path it took, or what it observed along the way. All of that is stored in external data structures that the model reads from but cannot modify. At each step, the system provides a verified snapshot of the agent's position, its surroundings, and whatever past observations are relevant. The model's job is reduced to interpretation and decision-making in natural language. It only handles semantics. All LLMs only hallucinate because there is something to "summarize", but if we feed it very short sentences, it cannot hallucinate.


Let use a 2D maze or any environment divisible into a grid as an example. Here, three structures maintain the agent's spatial experience:

A **route table**, keyed by coordinates, storing the exact path taken to reach each visited location. When the agent needs to return somewhere, the system hands it the path. The model does not plan routes.

**Observation chains**, where each location maintains a short linked list of what the agent perceived there, each node carrying raw text, semantic tags, and an embedding vector. The chain preserves temporal order, so the agent can distinguish what it saw on its first visit from what it saw later.

**Semantic retrieval** over those embeddings, so the agent can answer questions like "have you seen anything dangerous?" without needing to know which cell the danger was in.

A lightweight classifier model scans each input before the primary agent responds, determining which memory component to query. This keeps prompts short. The prompt at each step contains only position, perception, and the single relevant memory retrieval. Its length stays bounded no matter how long the agent has been navigating.

The architecture seems to be model-agnostic. Please check some simple trees I have demonstrating this idea in the repo.
