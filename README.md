# AI infrastructure presales toolkit

A worked example for three conversations: what the customer needs, which GPU configurations are worth testing, and what evidence would justify a decision.

## The case

A fictional hospital operations team wants to run a private 70B assistant that summarizes **synthetic** administrative documents for staff. It does not make clinical decisions or use patient records. The team asks for a card count. The right first answer is a shortlist and a test plan: fitting the model into memory does not tell us how many requests it can handle at the latency target.

| Start here | Use it to |
|---|---|
| [Discovery worksheet](discovery.md) | Turn the initial request into a workload, service targets, and a list of decisions with owners. |
| [GPU decision table](gpu-decision.md) | See why H200 SXM TP2 is the first configuration to test, and what would change the shortlist. |
| [PoC acceptance plan](poc-acceptance.md) | Agree on the test and pass criteria before collecting results. |

**Status:** Illustrative estimates only. The model architecture, traffic, latency targets, and quality gates are assumptions for this exercise. No PoC was run, and nothing here is a quote, a deployment claim, or a compliance assessment.

## Related work

The [inference GPU sizing Skill](https://github.com/C1806xixi-lin/C1806xixi-lin/blob/main/skills/inference-gpu-sizing/README.md) contains the reusable calculator. The [500 PFLOP/s sizing case](https://github.com/C1806xixi-lin/C1806xixi-lin/blob/main/case-studies/healthcare-ai-compute-center.md) covers theoretical dense FP16 fleet sizing, a different problem from meeting latency under application load.
