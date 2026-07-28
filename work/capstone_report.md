# Capstone Report — Content Refresh Opportunity Scoring

- **Author:** Aditya Garg
- **Lane:** Content Refresh Opportunity Scoring
- **Repo:** https://github.com/adityag30/FlyRank-internship-ML
- **Date:** July 2026

## 0. Abstract

This project investigates how search performance and engagement metrics can be used to prioritize web pages for manual content review and refresh. The analysis uses the FlyRank ML Internship dataset, specifically the March 2026 public release, with aggregated Google Search Console and Google Analytics features. A Decision Tree classifier was trained and compared against a transparent rule-based baseline using the same train/test split, while also auditing the workflow for potential label leakage. The Decision Tree achieved stronger performance than the baseline, although the observed metrics should be interpreted carefully because the target was derived from some of the available features. The final output is a ranked action queue that helps SEO specialists and content editors identify pages that may benefit from manual review.

## 1. Problem framing

This project supports the decision of which content pages should be prioritized for manual review and refresh. The unit of analysis is an individual content page represented by aggregated search and engagement metrics. The output is a ranked recommendation that classifies pages into action categories such as **Refresh Immediately**, **Review Content Quality**, **Monitor Performance**, or **No Immediate Action**.

The recommendations are intended for SEO specialists and content editors who must decide where to spend limited editorial effort. A false positive may cause unnecessary work on a page that does not need attention, while a false negative may leave a high-impact page unreviewed and reduce the opportunity for improving user engagement or search visibility.

Machine learning is appropriate because multiple search and engagement signals interact in ways that are difficult to capture with a single hand-written rule. The model is used as a decision-support tool rather than an automated decision maker.

## 2. Data safety

The analysis uses the public FlyRank ML Internship dataset for the March 2026 release. Data were queried from the DuckDB warehouse and aggregated at the content-page level using Google Search Console and Google Analytics metrics.

The model uses only aggregated numerical features, including impressions, clicks, average search position, sessions, engaged sessions, click-through rate (CTR), and engagement rate.

Several fields were intentionally excluded. Label-derived variables such as `trend_direction` and `trend_pct` were not used because they can leak information about the prediction target. Pseudonymous identifiers were not used as model features because they do not represent meaningful page characteristics and could introduce unintended shortcuts. No client names, URLs, search queries, or other identifying information are included anywhere in the repository or generated outputs.

All published results are based on aggregated statistics and are safe for public release.

## 3. Baseline

Before training a machine learning model, a transparent rule-based baseline was developed to provide a simple and interpretable reference. The baseline identifies pages with high search visibility but relatively poor click-through performance using manually selected thresholds.

The baseline was evaluated on the same train/test split and using the same evaluation metrics as the Decision Tree model. This provides a fair comparison because both approaches are assessed on identical data.

Although the baseline captures obvious refresh opportunities, it cannot model more complex interactions between search visibility and user engagement signals. The Decision Tree achieved stronger performance on the evaluation split, demonstrating the benefit of learning feature interactions automatically.

## 4. Model/analysis

A Decision Tree classifier was selected because it produces interpretable decision rules that can be inspected and explained to non-technical stakeholders. The model predicts whether a page should be prioritized for manual review based on search visibility and engagement metrics.

The feature set consists of:

- Google Search Console impressions
- Google Search Console clicks
- Average search position
- Google Analytics sessions
- Google Analytics engaged sessions
- Click-through rate (CTR)
- Engagement rate

Features derived directly from the target definition or containing trend labels were intentionally excluded to reduce leakage risk.

The prediction target is a binary proxy indicating pages with high search visibility but relatively low click-through performance. This proxy was designed for experimentation and should not be interpreted as ground truth for content quality.

## 5. Evaluation

The dataset was divided into training and testing subsets using a random train/test split because client identifiers required for grouped validation were not available in the released dataset.

Model performance was compared with the rule-based baseline using the same evaluation split. Standard classification metrics and confusion matrix analysis were used to assess performance.

The Decision Tree outperformed the baseline on the evaluation data. However, because the prediction target is derived from some of the available features, the measured performance should be interpreted as an upper bound rather than an estimate of real-world production performance.

Most classification errors occurred near the manually defined threshold separating positive and negative examples, suggesting that borderline pages are naturally more difficult to classify than clearly high- or low-performing pages.

Decision Tree Precision@50: 0.74
Rule-based baseline Precision@50: 0.66

## 6. Interpretation

The Decision Tree primarily relied on search visibility and click-through behaviour when prioritizing pages for review. Pages with high impressions but relatively low CTR were consistently assigned higher priority, while engagement metrics provided additional information for distinguishing between pages with similar search performance.

Feature importance analysis showed that impressions, CTR, and engagement-related metrics contributed most strongly to the model's decisions. This aligns with the intuition that pages attracting substantial search visibility but generating relatively few clicks deserve closer editorial attention.

The analysis also demonstrated the importance of auditing for label leakage. High evaluation scores alone should not be interpreted as evidence that the model will generalize equally well to future unseen data when the target is partially derived from available features.

## 7. Recommendation

The final output of this project is a ranked action queue that supports manual editorial decision making.

The recommended priority order is:

1. Refresh Immediately
2. Review Content Quality
3. Monitor Performance
4. No Immediate Action

Each recommendation is accompanied by a reason code to improve transparency and support human review. Editors can use this ranked queue to prioritize limited resources toward pages that may offer the greatest opportunity for improvement.

These recommendations should be treated as decision-support rather than automated instructions. Final editorial decisions should also consider business objectives, search intent, and qualitative content review.

## 8. Reproducibility

All notebooks, generated figures, and output files are included in this repository. The analysis was performed using DuckDB, Python, pandas, scikit-learn, matplotlib, and Jupyter Notebook.

The workflow can be reproduced by cloning the repository, installing the required Python packages, and executing the notebooks in order:

1. Data preparation
2. Feature engineering
3. Baseline construction
4. Model training
5. Evaluation
6. Ranked recommendation generation
7. Capstone report

The Decision Tree model was trained using a fixed `random_state=42` to improve reproducibility. Generated artifacts, including figures and the ranked action queue, are committed to the repository so that reported results can be independently inspected.

## 9. Acknowledgments & data credit

This project was built using the **FlyRank ML Internship dataset**. The dataset and internship materials were provided for educational purposes through the FlyRank Machine Learning Internship.

Data source: https://internship.flyrank.ai

---
