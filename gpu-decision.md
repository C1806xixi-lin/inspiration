# GPU decision table

**First PoC candidate: H200 SXM at TP2.** It keeps BF16 weights and offers more memory margin than H100 SXM at the same TP width. Test H100 SXM at TP4 if that is the available configuration, and take the L40S route only if the team accepts testing quantized weights. This is a recommendation for test order, not a purchase recommendation.

## Memory screen

The [synthetic workload](discovery.md) assumes a dense 70B model, BF16 KV, eight active sequences and a combined input/output limit of 8,192 tokens. With 80 layers, eight KV heads and head dimension 128:

- BF16 weight payload: `70e9 × 2 = 140 GB`.
- BF16 KV payload: `2 × 80 × 8 × 128 × 8192 × 8 × 2 = 21.47 GB`. The first 2 is for keys and values; the last 2 is bytes per element.
- Use the same decimal GB units throughout. Screen against 85% of nameplate memory per card, allowing 15% as an illustrative allocation reserve. The calculation assumes even weight and KV sharding; actual allocations may be uneven or replicated. Check the runtime-reported capacity on the exact hardware. Runtime buffers, quantization metadata, fragmentation and graph allocations still need measurement.

| Candidate | Vendor specs per GPU | Candidate layout and estimated payload/card | What could rule it out |
|---|---|---|---|
| H200 SXM | 141 GB HBM3e; 4.8 TB/s | BF16 TP2: 80.74 GB vs. 119.85 GB screening budget. Single GPU fails the screen even for weights alone at this reserve. | Availability, platform fit or no acceptable service capacity at tested batch sizes. |
| H100 SXM | 80 GB HBM3; 3.35 TB/s | BF16 TP4: 40.37 GB vs. 68 GB. TP2 fails the memory screen at 80.74 GB/card. | Four-card placement and collective communication may outweigh sticker-price savings; verify with the actual host fabric. |
| L40S PCIe | 48 GB GDDR6; 864 GB/s; no NVLink | 4-bit weight payload only: 35 GB before metadata. With BF16 KV, TP2 is 28.24 GB/card vs. 40.8 GB. This does not prove kernel support or quality. | Quantization quality, format/kernel compatibility, PCIe topology, or latency against the same workload. |

Vendor specifications: [NVIDIA HGX AI Factory components](https://docs.nvidia.com/enterprise-reference-architectures/hgx-ai-factory-h100-h200-b200/latest/components.html), [NVIDIA L40S specifications](https://www.nvidia.com/en-us/data-center/l40s/#specifications). These are hardware specs, not inference measurements.

## Decision rule

First reject configurations that do not load and sustain the tested context and active sequence cap. Then score each survivor against the [same PoC acceptance plan](poc-acceptance.md). Compare the cost of the **whole deployment** after measuring SLA-qualified throughput per replica and validating scale-out. A cheaper card can cost more once replication, power, and host requirements are included.
