# RustyLlama

**An ambitious Rust reimplementation of [llama.cpp](https://github.com/ggml-org/llama.cpp).**

RustyLlama aims to bring model loading, inference, tensor-format conversion, and quantization into a Rust-native project, with the long-term goal of exposing these capabilities through a single executable.

> **Status: early development / experimental.** This is not yet a complete llama.cpp replacement. The status below separates existing work from unfinished functionality.

## Project goals

- Run supported local language models.
- Convert Hugging Face checkpoints and other tensor formats into GGUF.
- Convert between supported model and tensor formats.
- Quantize weights into supported GGML/GGUF formats.
- Inspect and validate GGUF files.
- Provide a unified command-line interface.

## Current implementation

| Component | Status | Details |
|---|---|---|
| GGUF reader | Early implementation | Reads GGUF v2/v3 headers, metadata, tensor descriptors, and storage information. |
| GGUF inspection | Early implementation | CLI support for inspecting GGUF files and listing known tensor types. |
| GGUF writer | Partial | Writes GGUF v3 output for the implemented SafeTensors conversion path. |
| SafeTensors reader | Partial | Reads Hugging Face configuration and SafeTensors shards; validates headers, tensor ranges, and expected byte sizes. |
| SafeTensors → GGUF | Experimental | Converts supported tensors and maps selected architecture metadata and tensor names. |
| GPT-OSS conversion | Experimental | Handles selected GPT-OSS tensor names, MXFP4 block repacking, and gate/up expert splitting for a supported checkpoint layout. |
| Quantization | Partial | F32/F16 input support and F32, F16, Q4_0, Q4_1, and Q8_0 output paths. Unsupported existing quantized tensors are preserved rather than requantized. |
| Inference/runtime | Not implemented | Model execution, token generation, and inference backends remain future work. |
| Tokenizer conversion | Not implemented | Hugging Face tokenizer assets and metadata are not yet fully imported. |

### Recognized conversion architectures

The experimental SafeTensors converter includes architecture mappings for:

- Llama
- Mistral
- Qwen2
- Qwen3
- Gemma
- Gemma2
- GPT-OSS

Recognition does not imply complete support for every checkpoint variant or tensor layout. Conversion coverage remains incomplete. Validate output against the source checkpoint and a known-good implementation before relying on it.

## CLI direction

The intended command interface includes:

```sh
llama-rs inspect model.gguf
llama-rs validate model.gguf
llama-rs types
llama-rs quantize input.gguf output.gguf Q4_0
llama-rs convert-safetensors ./hf-model ./model.gguf
```

These examples describe the intended interface; exact command availability may change as the project is integrated and tested.

## Roadmap

1. **Harden conversion:** compile and test the converter, add tokenizer import, and compare generated GGUF files against upstream conversion results.
2. **Expand architecture coverage:** add more architecture-specific mappings and transformations, including additional MoE, vision, and audio layouts where feasible.
3. **Expand quantization:** implement more GGML quantization types, importance-matrix workflows, and mixed quantization profiles.
4. **Build inference:** add tokenizer execution, model loading, CPU inference, and optional accelerated backends.
5. **Unify the executable:** expose conversion, quantization, inspection, validation, and inference through one CLI.

## Limitations

- This is experimental software, not a feature-equivalent replacement for llama.cpp.
- SafeTensors conversion covers selected architectures and tensor layouts only.
- Tokenizer assets are not yet fully converted.
- Quantization is limited to the formats listed above.
- No inference engine or hardware backend is implemented yet.
- The Rust build and tests have not been verified in the development environment. This snapshot is not release-ready.

## Development

With the Rust toolchain installed, run these checks from the project root:

```sh
cargo fmt --check
cargo test
cargo build --release
```

## Relationship to llama.cpp

RustyLlama is an independent Rust reimplementation inspired by llama.cpp. It is not an official llama.cpp project and is not affiliated with its maintainers.

## License and attribution

RustyLlama-authored code is licensed under the **MIT License**; see [LICENSE](LICENSE). This permits use, modification, redistribution, and commercial use subject to retaining the copyright and license notice.

RustyLlama is an independent project and is not an official llama.cpp project or affiliated with its maintainers. The llama.cpp 0.9.0 source release is MIT-licensed by the ggml authors and is used as a compatibility and behavioral reference. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for upstream attribution and the license text. Any upstream code or third-party component incorporated in future releases must retain its applicable notices and license terms.

The MIT license for RustyLlama does not grant rights to model weights, tokenizer assets, datasets, or other files converted by the program. Those materials remain subject to their own licenses and terms.