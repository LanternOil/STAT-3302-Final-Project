# Himalayan Climbing Expeditions — Slide Notes

---

## Exploratory Analysis

_This slide likely contains a chart or visualization — reference it directly as you speak._

> "Before building any model, we explored the data to understand what we're working with. We looked at how success rates vary across our key predictors — things like altitude, oxygen use, season, and team size — to get a sense of what relationships might exist and whether any of them looked non-linear."

---

## Exploratory Analysis Continued

> "When we plotted success rate against peak height, we expected a simple downward trend — higher peaks, lower success. But that's not what we found.

> As you approach 8,000 meters, success rises sharply again at the most extreme altitudes. That last part is counter-intuitive — why would success rates go _up_ at the deadliest elevations?

> Our explanation is self-selection. Peaks above 8,000 meters — the so-called death zone — are only attempted by elite, highly experienced, well-funded teams. The average climber doesn't attempt Everest. So the pool of climbers at those extreme altitudes is not representative — it's skewed toward the most capable people with the best equipment. That filters out a lot of the failures you'd otherwise expect.

> This non-linear pattern is also why we included a quadratic height term in our model, rather than assuming a straight-line relationship between altitude and success."

---

## Model Specification

> "To answer our research questions, we fit a logistic regression model — since our outcome is binary: success or failure. Each expedition is modeled as a Bernoulli trial, and we use a logit link.

> We included seven predictors. The most important ones to highlight: supplemental oxygen use, peak height centered at the mean, and a quadratic height term to capture the non-linear pattern we saw in the exploratory analysis. The centerpiece of the model is the interaction between oxygen use and peak height — that's directly testing our central hypothesis that oxygen matters _more_ at extreme altitudes."

### Why Kilometers and Centering

> "Before getting into the polynomial terms, it's worth explaining two choices we made about how height is represented in the model.

> First, we converted height from meters to kilometers. The original variable was in meters, where values range roughly from 6,000 to 8,849. If we left it in meters, the model's coefficients would describe the effect of a one-meter increase in altitude — a change so small it's practically meaningless and produces coefficient estimates close to zero that are hard to interpret. Switching to kilometers means each unit is a one-kilometer increase, which is a meaningful increment in the context of high-altitude climbing.

> Second, we centered the height variable at its mean — that's what the '_C' suffix stands for. Centering shifts zero to the average peak height in our dataset, rather than true zero (which would be sea level — a nonsensical baseline for this analysis). This matters for two reasons: it makes the intercept interpretable as the log-odds of success for an average-altitude expedition, rather than a hypothetical expedition at sea level. More importantly, it reduces multicollinearity between the linear and quadratic height terms. Without centering, HEIGHTM_KM and HEIGHTM_KM² are highly correlated — both trend upward with altitude — which inflates standard errors and destabilizes coefficient estimates. Centering breaks that correlation and makes the estimates more reliable."

### Linear vs. Polynomial Height Terms

> "We included peak height twice in the model — once as a straight linear term, and once squared as a quadratic term. Here's why they do different things.

> The linear term, HEIGHTM_KM_C, captures the overall direction: as height increases, what generally happens to success odds? On its own, a linear term assumes the effect of altitude is constant — every extra kilometer of height has the same impact whether you're going from 6km to 7km or from 7km to 8km.

> But we just saw in the exploratory analysis that the relationship isn't a straight line — success rate dips, then rises again at extreme altitudes. A straight line can't capture that shape. That's where the squared term, HEIGHTM_KM_C², comes in. Adding it lets the model curve — it can bend up or down across the altitude range, which matches what we actually observed in the data.

> Together, the two terms give the model the flexibility to fit that non-monotonic pattern rather than forcing a relationship that doesn't match reality. And we formally confirmed the quadratic term belongs — adding it dropped the AIC from 661 to 656 with a p-value of 0.005, so it's a statistically meaningful improvement in fit."

---

## Model Results — Odds Ratios

> "A few key takeaways from the results. First, at average altitude, using supplemental oxygen multiplies your odds of success by about 14.5 — that's already massive. Each additional kilometer of height, without oxygen, cuts your odds by about 91%. More high camps and larger teams both significantly increase success odds. Season matters too — Spring expeditions have significantly lower success rates than Autumn.

> But the headline number is the interaction term: an odds ratio of 27.85 per kilometer of altitude. That means oxygen becomes dramatically more beneficial as you go higher. We'll dig into that next."

---

## Interaction Term Justification

> "We didn't just include the interaction because it seemed interesting — we formally tested whether it belongs in the model. A likelihood ratio test comparing the additive model versus the interaction model gives a p-value of about 0.000005. AIC also prefers the interaction model by nearly 19 points. Both criteria agree: the interaction significantly improves model fit and we're justified in keeping it."

---
