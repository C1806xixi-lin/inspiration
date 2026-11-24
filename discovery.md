# Discovery worksheet

Use the first conversation to check which decision the customer needs to make. For this example, the decision is whether to fund a private 70B pilot for an administrative summarization tool, not how many GPUs to buy today. Replace the example answers and name the people who can confirm them.

| Ask | Example answer (assumed) | Who confirms |
|---|---|---|
| Who uses it, for what, and what is out of scope? | Hospital operations staff summarize mock administrative notices and procedure documents using fictional names. Diagnosis, treatment advice, real patient records, and automated decisions are out of scope. | Operations lead |
| Which exact checkpoint, runtime, and precision? Is quantization acceptable? | Synthetic dense 70B grouped-query attention model: 80 layers, eight KV heads, head dimension 128. Start with BF16 weights and KV. Consider 4-bit weights only if they pass the same quality gate against the unquantized baseline. Actual checkpoint, runtime and compatible kernels are open. | ML owner |
| What is a typical request? What is the longest supported one? | PoC traffic: 80% at 2,048 input / 256 output tokens; 20% at 7,680 input / 512 output tokens. 8,192-token combined maximum. Use the same tokenizer across candidates. | Application owner |
| What load and latency would make the pilot usable? | Starting targets: 2 requests/s sustained, 4 requests/s short burst, p95 TTFT ≤2 s, p95 TPOT ≤100 ms. Begin with a cap of eight active sequences per replica; it is not an RPS guarantee. | Application owner |
| Where can model, data and logs live? | Private network and synthetic-only fixtures. No prompt or completion bodies in logs or telemetry. Review deployment and data boundaries before opening a real-data pilot. | Security and platform owners |
| What infrastructure is already available? | Illustrative shortlist: H200 SXM, H100 SXM, L40S PCIe. Actual host SKU, topology, power, inventory, and trial access are open. | Infrastructure owner |
| What will actually change the purchasing decision? | A repeatable PoC at a signed-off workload, acceptable quality, and a comparable cost per SLA-qualified request using approved prices. Prices are not assumed. | Project sponsor |

## Before the PoC

Confirm the exact checkpoint, allowed weight formats, target site, test-data owner, and who signs off. Have the application owner approve the load profile and timeout policy. If they cannot yet give a traffic forecast, keep the fixed rates as a sensitivity test rather than a demand estimate. Take the resulting brief to the [GPU decision table](gpu-decision.md).
