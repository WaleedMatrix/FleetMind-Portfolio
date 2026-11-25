# Release security audit

## Original repository audit

A reachable Git object in the original repository contains a 4.3 KB PostgreSQL dump first added in commit `4aa9c54`. The public repository starts at a clean source import. The dump path and the original history are absent from its reachable publication history.

The original local history review enumerated 635 reachable objects and scanned 391 blob versions for known private key, cloud, GitHub, LLM token, JWT and database URL formats. Credential-pattern hits were limited to three versions of the environment template with placeholder connection strings. The file-name scan found the dump. Pattern coverage is limited. If the dump was shared previously, review exposure privately, including any credentials that could be present, and rotate affected secrets. [Publication repository](deployment.md#publication-source)

## Dependency scans

The [Milestone 11B report](../reports/dependency_security_m11b.json) records 19 original entries across 12 unique vulnerabilities. A fresh local Python audit inspected 184 distributions, including optional MLOps and knowledge packages. It reported zero findings in the 182 auditable distributions and two skips. FleetMind is installed editable, and PyPI's advisory lookup cannot match the installed `torch==2.9.1+cpu` build. A separate normalized scan of `torch==2.9.1` returned five entries for four unique CVEs. The fresh npm lockfile audit returned zero findings. For the hosted image, a separate audit found zero findings and zero skips across all 29 installed distributions, including 27 application pins plus Packaging and Wheel from the pinned base image. These results concern published dependency advisories at validation time. They do not certify the code or the base operating system.

| Issue in the local optional CPU environment | Exposure and fixes |
| --- | --- |
| `PYSEC-2026-139` / CVE-2026-4538 | Unsafe loading of a crafted PT2 archive. The scanner lists no fix for this entry. The upstream bulletin lists PyTorch 2.10.0 for the PT2 fix. [Upstream security bulletins](https://github.com/pytorch/pytorch/security/advisories) |
| `PYSEC-2025-194` / CVE-2025-3000 / GHSA-rrmf-rvhw-rf47 | Memory corruption via TorchScript in versions <= 2.12.1. The advisory lists 2.13.0. [Advisory](https://github.com/advisories/GHSA-rrmf-rvhw-rf47) |
| `PYSEC-2025-195` / CVE-2025-3001 / GHSA-qfhq-4f3w-5fph | Memory corruption via torch.lstm_cell in versions < 2.10.0. The advisory lists 2.10.0. [Advisory](https://github.com/advisories/GHSA-qfhq-4f3w-5fph) |
| `PYSEC-2026-2286`, `PYSEC-2026-1856` / CVE-2026-24747 / GHSA-63cw-57p8-fm3p | Crafted checkpoints submitted to `torch.load(..., weights_only=True)`. Versions < 2.10.0 are affected. The advisory lists 2.10.0. [Advisory](https://github.com/advisories/GHSA-63cw-57p8-fm3p) |

An official Windows CPython 3.11 CPU wheel exists for 2.13.0. The download stalled in the validation environment, so the compatibility comparison could not run. The existing local environment keeps the tested pin. Optional local retrieval uses a pinned MiniLM snapshot, offline loading by default, safetensors, and remote code disabled. Use trusted model directories. Do not load third-party TorchScript, pickle checkpoints, or PT2 archives in the FleetMind environment. Users who need only local dashboards or portfolio inference can omit the retrieval stack entirely. The portfolio runtime allowlist and image exclude PyTorch, Transformers, Sentence Transformers, live SHAP, database clients, Kafka, MLflow and LLM providers. See [runtime and release validation](release-validation.md).

## Public request boundaries

The public portfolio rejects private API routes and uses recorded scores. If configured, the optional hosted inference bridge uses a fixed HTTPS Render origin, accepts only the inference path, caps payload size and forwards no browser session credentials. The service enforces schema, size, upload time, one active inference and a process-wide attempt budget. The shared budget can be exhausted by an individual caller and resets on process restart. The recorded profile remains available during that condition. Private application authorization and auditing are part of [the full local deployment](security.md).
