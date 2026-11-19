# pplx-decider-flash-v1.3 weights mirror

Independent, public GitHub Releases mirror of [Perplexity's pplx-decider-flash-v1.3](https://huggingface.co/perplexity-ai/pplx-decider-flash-v1.3). Contains distribution scripts, **not** weights committed to Git history.

## Publish the model

1. Open [GitHub Actions: Publish weights](https://github.com/caderea/pplx-decider-flash-v1.3-weights/actions/workflows/publish-weights.yml).
2. Select **Run workflow**, retaining the pinned default upstream revision `d06fb3f2d97e0a52325a0d463c450cf0338f44fe` and tag `pplx-decider-flash-v1.3`.
3. The publisher creates a draft release, streams model data into 1 GiB ZIP segments, uploads each segment, frees runner disk space, and uploads `manifest.json`. It publishes the release **only after** all files upload.
4. Verify the results on the [Releases page](https://github.com/caderea/pplx-decider-flash-v1.3-weights/releases).

If the Hugging Face snapshot requires authentication, add an authorized `HF_TOKEN` repository Actions secret; ensure the token has download access to the upstream model. **Never commit the token.** The workflow can fail if Hugging Face limits access or permissions are insufficient.

If a run fails midway, **rerun the workflow using the same revision and tag**. The publisher will reuse previously uploaded chunks only when their size and SHA-256 digest match the regenerated chunks. It also resumes Hugging Face downloads using HTTP Range after transient connection failures. Existing draft Releases are reused and are only published once the full manifest and every chunk have been processed. If a reused asset differs, the workflow fails safely rather than overwriting it. Do not manually publish an incomplete draft.

## Download on another computer

Requires Git and Python 3.10+; does not require `huggingface_hub`, PyTorch, or Git LFS **just to download**.

```bash
git clone https://github.com/caderea/pplx-decider-flash-v1.3-weights.git
cd pplx-decider-flash-v1.3-weights

python3 scripts/weights.py download --tag pplx-decider-flash-v1.3 --out ./models/pplx-decider-flash-v1.3
```

This fetches the manifest and ZIP chunks from GitHub Releases, verifies SHA-256, reassembles the archive, extracts the original model files and verifies every file against the manifest. Repeat downloads reuse valid cached parts. The downloader currently needs ample free disk space for the chunks, temporary assembled ZIP and extracted files.

**Important:** the command works **only after** a successful GitHub Release has been published. Model inference additionally requires the dependencies and supported hardware described in the upstream model card. This is a full native checkpoint with inference code, not a LoRA-only adapter.

## Provenance and license

Original model: Perplexity AI, [perplexity-ai/pplx-decider-flash-v1.3](https://huggingface.co/perplexity-ai/pplx-decider-flash-v1.3), Apache-2.0 as labeled on Hugging Face. This repository is not affiliated with Perplexity. Respect upstream redistribution terms and preserve all upstream LICENSE and NOTICE attribution files. The generated manifest records the immutable upstream commit revision and SHA-256 hashes.

## Operational notes

- Each release chunk is 1 GiB, below GitHub's per-asset size limit.
- The GitHub Actions publisher is designed to keep only one chunk on the runner at a time.
- The downstream download script uses only the Python standard library.
- `git clone` fetches the scripts, not the weights; weight files live in the Release assets.
