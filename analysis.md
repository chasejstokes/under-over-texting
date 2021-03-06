# Introduction

This file contains and describes examples of analysis from this project.

Please cite:

Stokes, C., Setlur, V., Cogley, B., Satyanarayan, A., & Hearst, M. (2022). Striking a Balance: Reader Takeaways and Preferences when Integrating Text and Charts. *IEEE Transactions of Visualization and Computer Graphics*.

# Setup

Data cleaning and reshaping will not be included in this file, but can be found in [balanceText.Rmd](data_analysis/balanceText.Rmd).

## Required libraries

To ensure all the packages required are installed, the following script will install and load the necessary packages.


```
packages = c("tidyverse", "ggplot2", "plyr", "car", "lme4", "stringr", "ggpubr", "PMCMRplus", "irr")

package.check <- lapply(
  packages,
  FUN = function(x) {
    if (!require(x, character.only = TRUE)) {
      install.packages(x, dependencies = TRUE)
      library(x, character.only = TRUE)
    }
  }
)
```

# Research Question 1 

**What are readers preferences when viewing charts with different amounts and semantic levels of text? How does this vary based on overall preference for textual or visual information?**

To investigate this question, we conducted Friedman tests for the rank-order data and post-hoc Nemenyi's tests to examine pairwise comparisons. We also calculate the average rank positions for each variant. This process is the same for H1a through H1d. An example of H1a is shown below.


```
# friedman test
friedman.test(y=h1a$rankPosition, groups = h1a$rankVariant, blocks = h1a$participantID)

# posthoc nemenyi test
frdAllPairsNemenyiTest(y=h1a$rankPosition, groups = h1a$rankVariant, blocks = h1a$participantID)

# average rankings
aggregate(rankPosition ~ rankVariant, h1a, mean)

```

# Research Question 2

**How do the different semantic levels of text affect the type of information included in readers' takeaways? Further, when do readers include information in their takeaways that are not found either in the chart or the text? Which semantic levels of text do readers rely on when they form their takeaways?**

We used logistic regression to analyze this question to determine *predicting* factors for the elicitation of each semantic level in a takeaway. For each section, we compare the likelihood of a participant making a takeaway in the semantic level of the section (e.g., `isTakeawayL1`) when the annotation is present on the chart (`onChartSemanticLevel = 1`) in comparison to another level (e.g., `onChartSemanticLevel = 2`). 

For each model, we are looking for a single value which will indicate the specific comparison we are looking to make. Because the data is quantitative (different semantic levels) rather than qualitative, this requires twelve total comparisons (3 for each semantic level). Each model is named `takeaway_[target level]_to_[comparison level]` (e.g., `takeaway_L1_to_L2`). The value of interest is in the row `as.factor(onChartSemanticLevel)[target level]`. 

This balue indicates the significance of the factor. In other words, it indicates whether the presence of the target annotation (e.g., L1) has an effect significantly greater or lesser than the presence of the comparison annotation (e.g., L2) on the takeaway being in the semantic level of the target annotation (e.g., L1). 


```{r takeaway_L1_comparedto_L2}

takeaway_L1_to_L2 <- glm(formula = isTakeawayL1 ~ as.factor(onChartSemanticLevel), family = binomial(link = "logit"), data = h2a_L2)
summary(takeaway_L1_to_L2)
Anova(takeaway_L1_to_L2)
exp(coef(takeaway_L1_to_L2))[4]

```

In examining this question, we also compare the self-reported reliance on different forms of information. To do this, we conducted an ANOVA and pairwise t-tests to determine differences in reliance between different semantic levels and preference categories. An example comparing the different semantic levels is shown below.

```

# first, conduct the anova
anova_levels_h2cd <- aov(h2cd$takeawayInfoReliance ~ h2cd$semanticLevel)
summary(anova_levels_h2cd)

# then, pairwise t-test
pairwise.t.test(h2cd$takeawayInfoReliance, h2cd$semanticLevel, p.adjust.method = "bonf")


```

# Research Question 3

** How does the placement of text affect how the reader integrates the text with the chart?**

We used logistic regressions, as in RQ2, to determine predicting factors for the elicitation of each semantic level in a takeaway, this time focusing on position as the factor of interest. We compare the likelihood of a participant making a takeaway in the semantic level of the section (e.g., `isTakeawayL1`) when the annotation is positioned at a reference level (e.g., title) in comparison to the other positions. For this question, we are only interested in how the different positions compare to the title position, not to each other. An example for L1 is shown below.

```

position_L1 <- glm(formula = isTakeawayL1 ~ as.factor(annotationPosition), family = binomial(link = "logit"), data = h3)
summary(position_L1)
Anova(position_L1)
exp(coef(position_L1))

```

# End

This concludes the example analysis for "Striking a Balance: Reader Takeaways and Preferences when Integrating Text and Charts". Further analysis can be found in [balanceText.Rmd](data_analysis/balanceText.Rmd). Contact [Chase Stokes](mailto:cstokes@ischool.berkeley.edu) with any questions or requests for additional materials.
