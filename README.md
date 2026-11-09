# AI vs Traditional Translation | Localization Reporting in Power BI

**How is AI-first translation changing the cost, speed, and quality of localization, and why are some jobs late?**
A three-page Power BI report on 2,569 translation jobs across 13 languages, 6 vendors, and 4 workflows, January 2025 to October 2026.

> **Simulated data.** The data describes a fictional company and was generated to practice localization reporting. Patterns were built into the simulation, so the findings describe the simulation, not a real team.

**See the report:** [PDF export](Localization_AI_vs_Traditional.pdf)

## Key findings

- **AI adoption is rising and cutting cost.** Machine- or AI-first workflows handled 41.0% of words in 2025 and 55.9% in 2026. Average cost per word fell 16%, from $0.147 to $0.124. AI with light review costs $0.044 a word, against $0.195 for human translation.
- **The trade-off is quality, but it's narrowing.** At a quality threshold of 90, 100% of human jobs pass review, against 85.5% for machine translation with post-editing and 60.7% for AI with light review. AI's average quality score rose from 89.2 to 91.9, and post-edit distance fell from 19.9% to 16.3%.
- **Late jobs follow language and market, not vendor.** APAC jobs are on time 76.2% of the time, against 84.5% elsewhere, and every vendor sits between 67% and 77% on time within APAC. Thai was on time only 42.9% of the time in its first three months, against 76.9% afterwards.
- **Recommendation.** Route high-volume, lower-risk content to AI first, keep post-editing or human review for APAC languages and legal content, and seed glossaries and translation memory before launching a new market.

## Report pages

| Page | What it answers |
| --- | --- |
| AI adoption and cost | How the workflow mix, cost per word, and translation memory reuse changed over time |
| Effort, quality and vendors | Post-edit distance by language, turnaround and effort by workflow, vendor spend, and quality pass rates with an adjustable threshold |
| Delivery root cause | Where late jobs come from: on-time % by language and vendor, late jobs by language, and on-time % by region over time |
| Notes | About the data, and definitions for every metric |

## What I built

- **Data model:** a star schema with a Jobs fact table and Language, Vendor, and Date dimensions. All measures live in a dedicated `_Measures` table.
- **DAX:** 3 calculated columns and 19 measures, including AI-assisted share of words, word-weighted post-edit distance, an LQA pass rate driven by a what-if threshold parameter, vendor share of spend, month-over-month change, and an on-time % that hides cells with fewer than 10 jobs.
- **Localization metrics:** translation memory leverage, post-edit distance, LQA pass rate, SLA adherence (on-time %), and cost per word by workflow.
- **Design:** one fixed colour per workflow across every visual, set by a colour measure and conditional formatting, plus synced slicers across pages.

## Data model

`Language`, `Vendor`, and `Date` each filter `Jobs` one-to-many, with single-direction filtering.

## DAX highlights

```dax
Weighted edit ratio =
DIVIDE(
    SUMX(Jobs, Jobs[Edit ratio] * Jobs[Word count]),
    CALCULATE([Total words], NOT ISBLANK(Jobs[Edit ratio]))
)
```
Post-edit distance weighted by words, so a 10,000-word job counts more than a 50-word one. The denominator counts only machine and AI jobs.

```dax
LQA pass rate =
VAR t = [LQA pass threshold Value]
RETURN
DIVIDE(
    CALCULATE(COUNTROWS(Jobs), Jobs[LQA score] >= t),
    CALCULATE(COUNTROWS(Jobs), NOT ISBLANK(Jobs[LQA score]))
)
```
The threshold comes from a what-if parameter slider, so reviewers can test how pass rates change between 70 and 100.

```dax
On-time % (10+ jobs) = IF([Jobs] >= 10, [On-time %])
```
Returns a blank where fewer than 10 jobs sit behind a cell, so the language and vendor matrix doesn't invite conclusions from tiny samples.

## Data

Simulated with Python for a fictional company: 2,569 completed jobs with dates, language, market, business function, content type, workflow, vendor, word counts, translation memory matches, edit ratio, linguist hours, cost in CAD, and sampled quality scores.

## How to open

Download `Localization_AI_vs_Traditional.pbix` and open it in Power BI Desktop (free, Windows). A PDF export of the report is also in this repository.

## Files

| File | What it is |
| --- | --- |
| `Localization_AI_vs_Traditional.pbix` | The Power BI report |
| `Localization_AI_vs_Traditional.pdf` | PDF export of the report |

