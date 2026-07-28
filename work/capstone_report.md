# Capstone Report — Content Refresh Opportunity Scoring

- **Author:** Aditya Garg
- **Lane:** Content Refresh Opportunity Scoring
- **Repo:** https://github.com/adityag30/FlyRank-internship-ML
- **Date:** July 2026

## 0. Abstract

Large content websites often contain thousands of pages, making it difficult for content teams to decide which pages should be refreshed first. This project investigates how search performance and engagement metrics can be used to prioritize content refresh opportunities using the FlyRank ML Internship March 2026 public dataset. The analysis combines exploratory data analysis, feature engineering, leakage auditing, a transparent rule-based baseline, and an interpretable Decision Tree classifier to rank pages for manual review.

The Decision Tree was evaluated against the rule-based baseline using the same train/test split and achieved stronger ranking performance (Precision@50: 0.74 versus 0.66). Because the prediction target is derived from available search signals, the reported performance should be interpreted carefully and viewed as an upper bound rather than production performance. The final result is an explainable ranked action queue that supports SEO specialists and content editors in prioritizing manual content refresh decisions.

## 1. Problem framing

### Case Study

This project addresses a real content optimization problem faced by FlyRank: identifying which published pages should be reviewed and refreshed first when editorial resources are limited. Large content libraries often contain thousands of pages, making manual prioritization slow and inconsistent. The objective of this project is to support that prioritization process using historical search and engagement signals.

The unit of analysis is an individual content page represented by aggregated Google Search Console and Google Analytics metrics. The output is a ranked recommendation that classifies pages into action categories such as **Refresh Immediately**, **Review Content Quality**, **Monitor Performance**, or **No Immediate Action**.

The intended users are SEO specialists and content editors who need to decide where to focus limited editorial effort. A false positive may cause unnecessary work on a page that does not require changes, while a false negative may leave a valuable page unoptimized and reduce opportunities for improving search visibility and user engagement.

Machine learning is appropriate because multiple search and engagement signals interact in ways that are difficult to capture with simple threshold-based rules. The model is designed as a decision-support system that provides transparent recommendations rather than replacing human editorial judgment.

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

"The final output of this project is an explainable ranked action queue that supports manual editorial decision-making for FlyRank's content refresh workflow."
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
