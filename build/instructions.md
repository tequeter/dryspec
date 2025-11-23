# Instructions common to all AI runtimes

## Challenges

### Ignored authoring instructions

LLMs tend to ignore conciseness, size limits, "SHALL NOT have", and destructive editing instructions.

Workarounds:

- Emphasize them in the prompts.
- Run a critique step in the workflows to enforce what was ignored during authoring.

### Ignored "Ask clarifying questions" instructions

They must be embodied with concrete patterns in the authoring prompts, and the critique steps should look for hidden assumptions.

