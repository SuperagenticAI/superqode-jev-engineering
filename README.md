# superqode-jev-engineering

Use Jev context selection in the SuperQode TUI. Start in shadow mode to inspect
decisions, retrieve the retained evidence, then enable enforcement when ready.
The example uses a synthetic runbook and TypeSafe's hosted Jev API.

## Requirements

- SuperQode 2.5.5.
- An official TypeSafe API key.
- A coding provider/model with its own credentials, or a compatible local model.

Jev makes context decisions. Your coding model runs the conversation.

Install SuperQode if needed:

```bash
uv tool install superqode==2.5.5
```

## 1. Open SuperQode

Clone the repository and open it:

```bash
git clone https://github.com/SuperagenticAI/superqode-jev-engineering.git
cd superqode-jev-engineering
```

If you already have a clone, open its directory instead. Set your key in that
terminal and launch SuperQode:

```bash
export TYPESAFE_API_KEY="YOUR_OFFICIAL_TYPESAFE_KEY"
sq
```

The remaining steps happen in the TUI.

## 2. Connect your coding model

Enter:

```text
:connect
```

Choose your coding provider and model. Then enter these commands one at a time:

```text
:systemone shadow
:harness use ./demo-shadow.yaml
:pipy new
```

This enables the live SystemOne client and selects the PiPy context
configuration. Shadow mode proposes changes while preserving the existing
prompt. Enabling the client does not itself make an API request.

## 3. Read the runbook

Send this prompt, also available in [01-read-evidence.txt](01-read-evidence.txt):

```text
Use the read tool to read the entire runbook.txt file.
Then reply only "Runbook read". Do not modify files.
```

Wait for the response and check that the model used `read`. The runbook contains
roughly 9,500 characters. This latest tool result is protected as recent
evidence, so complete the next step before opening the inspector.

## 4. Ask a follow-up question

Send this prompt, also available in
[02-trigger-selection.txt](02-trigger-selection.txt):

```text
Explain stable evidence references in one sentence.
You do not need the detailed historical runbook. Use no tools.
```

The earlier read is now eligible for selection. Jev evaluates whether the task
needs its full text or whether an excerpt and retrieval reference are enough.

## 5. Inspect the context decision

After the response, enter:

```text
:context evidence
```

Select an evidence row and inspect:

- **Mode and selector:** `shadow` and `jev`.
- **Probability:** Jev's estimate that the full evidence is still needed.
- **Action:** `keep` or `excerpt_with_reference`.
- **Character counts:** actual context size and proposed size.

In shadow mode, actual character counts stay unchanged. The proposed count can
be smaller when Jev selects an excerpt. A keep decision is also valid.

A numeric probability with a reason such as `bounded_score` or
`retain_evidence` indicates a returned decision. A fallback such as
`scorer_failed` is not a successful live decision; the call count alone does
not establish success.

## 6. Retrieve the original evidence

With the row selected, choose **Open original**. Use **Next page** to read the
remaining pages. The final page contains:

```text
END_MARKER=context-original-intact
```

The retained evidence is retrieved by reference in bounded pages. Retrieval
checks current permissions. Use **Back** or Escape to return to the chat.

## 7. Enable enforcement

Stay in the TUI and enter:

```text
:harness use ./demo-enforce.yaml
:pipy new
```

Repeat the two prompts from steps 3 and 4, then enter `:context evidence`.

If Jev selects an excerpt, actual context characters decrease. **Open original**
still retrieves the retained evidence. If Jev selects keep, the full text
remains in context. Separate runs can produce different decisions.

## Configuration and usage

Both configurations use one candidate and at most one selector evaluation per
run. Additional turns can make further calls. Jev and your coding provider use
their normal billing. Character counts are not measured token or cost savings.

The configurations declare `jev-1.13.0`, also the hosted PiPy context client's
default in SuperQode 2.5.5. Check model configuration when upgrading. Threshold
suitability still needs evaluation for your tasks.

For the relationship to the wider Jev engineering guide, see [Scope](SCOPE.md).

## Troubleshooting

| What you see | What to do |
| --- | --- |
| No native context decision | Select the PiPy configuration and complete both prompts. |
| No eligible evidence | Check that `read` returned the complete runbook. An attachment or prose response is not equivalent. |
| `scorer_unavailable` | Set your key before launch, run `:systemone shadow`, then select the harness again. |
| `scorer_failed` | Check official API access, connectivity and authentication. Original evidence stays present. |
| HTTP 401 | Check that your key is valid for TypeSafe's official API. |
| `keep` with a numeric probability | A valid decision; the full evidence remains in context. |
| Evidence unavailable or denied | Check permissions and access to the original artifact store. |

## Contributing

Improvements to setup instructions, example tasks, reproducible failure cases
and independently graded comparisons are welcome. Keep credentials, `.env`
files and session state outside version control.

## References

- [SuperQode context hosting](https://docs.superqode.dev/advanced/pipy/)
- [TypeSafe primitives](https://docs.typesafe.ai/introduction)
- [TypeSafe confidence guidance](https://docs.typesafe.ai/confidence)
- [SuperQode engineering announcement](https://super-agentic.ai/resources/super-posts/superqode-workorders-jev-engineering-monty-pi-durable)

License: Apache-2.0.
