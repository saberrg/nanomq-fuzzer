# NanoMQ Fuzzing Research

Status: in progress senior capstone research. This fork should not be described as upstreamed or accepted into OSS-Fuzz unless a merged PR/link exists.

## Research Focus

This repository is used to study coverage-guided fuzzing of MQTT broker components, with a focus on whether LLM-guided agents can help synthesize useful libFuzzer harnesses for protocol parsing code.

The immediate target is NanoMQ's MQTT PUBLISH decoding path. The broader goal is a reproducible workflow for harness generation, compilation, sanitizer execution, coverage comparison, and crash triage.

## Current Fuzzing Assets

- `nanomq/tests/fuzz/pub_decode_fuzzer.c`: libFuzzer entry point targeting `decode_pub_message`.
- `nanomq/tests/fuzz/CMakeLists.txt`: builds fuzzing targets when `ENABLE_FUZZING=ON`.
- `nanomq/tests/fuzz/mqtt.dict`: MQTT token dictionary for guided mutation.
- `nanomq/tests/fuzz/corpus/`: seed inputs for MQTT v3/v5 publish packets.
- `nanomq/tests/fuzz/README.md`: current build and run notes.

## Build

```bash
mkdir build
cd build
cmake -DENABLE_FUZZING=ON \
  -DCMAKE_C_COMPILER=clang \
  -DCMAKE_CXX_COMPILER=clang++ \
  -DCMAKE_C_FLAGS="-fsanitize=fuzzer-no-link,address -g" \
  -DCMAKE_CXX_FLAGS="-fsanitize=fuzzer-no-link,address -g" ..
make pub_decode_fuzzer -j$(nproc)
```

## Run

```bash
./nanomq/tests/fuzz/pub_decode_fuzzer \
  -dict=../nanomq/tests/fuzz/mqtt.dict \
  ../nanomq/tests/fuzz/corpus/
```

## Evaluation Plan

- Harness compile success.
- Parser coverage reached.
- Unique sanitizer crashes.
- Time-to-first-crash.
- Reproducer minimization quality.
- Manual intervention required to move from LLM-generated harness to runnable target.
- Reproducibility across fresh checkout/build.

## Current Limitations

- Findings should be treated as local/in-progress unless linked to upstream PRs or public disclosures.
- OSS-Fuzz integration should be described as planned or experimental unless accepted upstream.
- This fork is research-facing and should be evaluated by reproducible build/run steps, not by unverified vulnerability claims.

## Upstream Attribution

NanoMQ is an open source MQTT broker maintained by EMQ/NanoMQ. This fork is used for research and fuzzing workflow development.

For upstream project details, see https://github.com/emqx/nanomq.
