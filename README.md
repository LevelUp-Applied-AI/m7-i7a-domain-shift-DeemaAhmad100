# Module 7 Week A — Integration Task: Domain-Shift Analysis

Apply your fine-tuned classifier (from Lab 7A, hosted on Hugging Face Hub) to the tech / entertainment news corpus and analyze the domain-shift behavior.

Full instructions: see the **Integration Task 7A guide** linked in TalentLMS.

## Quick start

```bash
pip install -r requirements.txt
cp .env.example .env       # then edit MODEL_HUB_ID
make smoke                 # CI substitute model on 5-row fixture
make apply                 # your real model on full 1,033-row tech-news corpus
```

## Model and Reproducibility

- **Hugging Face Hub model URL:** `https://huggingface.co/Deema100/m7-app-review-sentiment`
- **Reproducibility command:** 
  ```bash
  cp .env.example .env
  # Edit .env and set MODEL_HUB_ID=Deema100/m7-app-review-sentiment
  make apply
  ```

## Domain-Shift Analysis

The sentiment classifier in this repository was fine-tuned on app store reviews—short, opinionated texts written by users evaluating consumer software. These reviews typically exhibit strong sentiment signals: explicitly negative language for poor performance, glowing praise for quality, and mixed feedback for mediocre experiences. The training data vocabulary and context patterns are deeply rooted in the application review domain.

We now apply this app-review classifier to a corpus of tech and entertainment news articles—longer, more formal prose written by journalists covering technology trends, product announcements, and industry analysis. News articles employ different linguistic conventions, more nuanced sentiment expression, and subject matter (companies, markets, technical details) distinct from individual user experiences. This domain mismatch provides a natural test of model robustness and reveals whether the classifier has learned domain-specific artifacts rather than generalizable sentiment patterns.

The analysis measures prediction distribution, confidence calibration, and qualitative failure modes to quantify domain shift. We expect the model to show lower confidence, polarized class predictions, and systematic misclassification on neutral or complex articles where formal news language diverges from app-review style.

## Submission

Open a PR from `integration-7a-domain-shift` into `main`. Paste the PR URL into TalentLMS → Module 7 → Integration Task 7A. All tests must pass and both `domain-shift-analysis.md` and `README.md` must be complete.

---

## License

This repository is provided for educational use only. See [LICENSE](LICENSE) for terms.

You may clone and modify this repository for personal learning and practice, and reference code you wrote here in your professional portfolio. Redistribution outside this course is not permitted.
