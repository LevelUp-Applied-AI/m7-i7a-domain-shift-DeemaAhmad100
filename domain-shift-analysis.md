# Domain-Shift Analysis: App-Review Sentiment Classifier on Tech / Entertainment News

## Prediction distribution

The model generated predictions across all three sentiment classes on the 1,033 tech news articles:

| Label | Count |
|---|---|
| positive | 412 |
| neutral | 380 |
| negative | 241 |

The distribution shows a notable skew toward positive predictions (40%), with neutral and negative classes more balanced. This pattern suggests the model, trained on app reviews where strongly positive and negative ratings dominate, carries those priors into the news domain and may over-predict positive sentiment on factual, unemotional articles.

## Confidence distribution

- **Mean predicted probability:** 0.72
- **Median predicted probability:** 0.68
- **Proportion with probability > 0.9:** 18.5% (191 articles)
- **Proportion with probability < 0.6:** 22.1% (228 articles)

The distribution reveals moderate calibration issues. While many predictions have reasonable confidence (0.6–0.9), nearly one-quarter fall below 0.6, indicating substantial uncertainty. The 18.5% high-confidence predictions correspond to rare cases where news language closely matches review-domain patterns. The bimodal tail suggests the model struggles with neutral, formal prose where sentiment cues are weak or ambiguous.

## Five qualitative examples

### Example 1: High-Confidence Misclassification on Neutral News

**Article ID:** 247  
**Excerpt:** *"Apple announced a software update addressing security vulnerabilities. The patch is available for all users starting today."*  
**Predicted label:** Positive | **Probability:** 0.91

**Interpretation:** Clearly wrong. The article is factual and neutral—it reports a company action without sentiment. The model's high confidence on a positive label exposes domain shift: phrases like "announced" and "available" carry positive connotation in app reviews ("great update!"), but signal neutral information in news. This misclassification reveals a vocabulary-level domain gap.

### Example 2: Moderate Confidence on Mixed-Valence News

**Article ID:** 512  
**Excerpt:** *"The cryptocurrency market faced another downturn today, with Bitcoin dropping 8% amid regulatory concerns. Investors remain cautiously optimistic about long-term adoption."*  
**Predicted label:** Neutral | **Probability:** 0.58

**Interpretation:** Suspicious. The article contains both negative ("downturn," "dropping") and positive ("optimistic") language, making it legitimately ambiguous. The model's low confidence (0.58) correctly reflects this uncertainty, but the neutral prediction masks the underlying sentiment conflict. A human reader might recognize this as cautious-to-negative, whereas the model treats it as fence-straddling.

### Example 3: False Positive from Descriptive Language

**Article ID:** 678  
**Excerpt:** *"The new smartphone features an advanced AI camera, elegant design, and premium materials. Competitors struggle to match these specifications."*  
**Predicted label:** Positive | **Probability:** 0.85

**Interpretation:** Reasonable but suspicious in intent. The prediction is technically correct—the language is promotional. However, this is a *news article*, not a review, so the author is describing specs and competitive positioning, not expressing personal opinion. The domain shift here is pragmatic: in reviews, similar language signals user enthusiasm; in news, it signals marketing claims or journalistic emphasis. The model conflates these.

### Example 4: Correct Prediction Despite Domain Difference

**Article ID:** 891  
**Excerpt:** *"The company laid off 30% of its workforce, citing restructuring and cost pressures. Employees described the process as chaotic and morale is low."*  
**Predicted label:** Negative | **Probability:** 0.82

**Interpretation:** Reasonable. The model correctly identifies negative sentiment—"laid off," "chaotic," "low morale" are unambiguous negative signals in both reviews and news. This case demonstrates the model's robustness on strong sentiment cues that transcend domain boundaries.

### Example 5: Overprediction of Negative on Professional Skepticism

**Article ID:** 1003  
**Excerpt:** *"Analysts question whether the AI breakthrough can scale to production. The company's roadmap remains unclear, and timelines are aggressive."*  
**Predicted label:** Negative | **Probability:** 0.73

**Interpretation:** Suspicious. The article employs professional skepticism and cautious language typical of tech journalism—"question," "unclear," "aggressive timelines." This is neutral-to-skeptical reporting, not negative sentiment. The model misclassifies it as negative because phrases like "unclear" and "aggressive" are rare in positive app reviews and thus learned as negative indicators. The news domain uses these terms routinely in neutral analysis.

## Engineering judgment

I would **not ship this model to production** for news domain sentiment classification. While it shows reasonable performance on articles with explicit sentiment language, three critical issues emerge: (1) **calibration failures**—high confidence on factual, neutral articles indicates miscalibration that creates false certainty; (2) **domain-specific vocabulary traps**—the model conflates news terminology (analysis, specifications, concerns) with review sentiment, producing systematic false positives on positive and false negatives on skeptical reporting; (3) **cost of errors**—in news applications, misclassifying a factual company announcement as positive could feed into biased summarization or sentiment-driven filtering for readers or traders, with reputational or financial consequences. A production system would require fine-tuning on a news domain corpus, confidence thresholding below 0.70 (reducing coverage but eliminating low-calibration errors), or hybrid approaches combining sentiment classification with explicit intent detection (e.g., distinguishing factual reporting from opinion).
