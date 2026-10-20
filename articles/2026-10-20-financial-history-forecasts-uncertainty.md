# From Financial History to Forecasts: Making Uncertainty Visible

A finance team has a spreadsheet of monthly costs and needs to decide whether it can afford a new service region. The average cost is easy to calculate. The harder question is what that average can reasonably tell the team about the future. A tidy chart or a confident forecast can conceal missing records, volatile costs, and assumptions that have not been tested.

Financial forecasting turns historical information and explicit assumptions into estimates of future financial outcomes. Budgeting translates plans and expectations into resource allocations. Both support decisions, but neither makes the future certain. Good analysis shows what the data says, what the model assumes, and where judgment still matters.

This chapter’s subject spans expert judgment, quantitative forecasting, simulations, spreadsheet budgeting, and Python workflows. A useful way to connect those practices is to treat data quality and uncertainty as part of the forecast, not as cleanup tasks performed after the headline number is chosen.

## A forecast is a conditional estimate, not a fact

A forecast answers a conditional question: given available information and a set of assumptions, what future outcomes are plausible? Historical revenue may reveal trend or seasonal patterns, while a model may extend those patterns into future periods. A regression can relate an outcome to explanatory variables. Expert judgment can help when a product or market has little usable history. Simulation can show how a forecast changes when uncertain inputs vary.

These approaches answer different questions. Time-series methods use the ordering and temporal structure of observations. Causal or explanatory models relate an outcome to other variables, but a relationship in observed data does not by itself establish that changing one variable will cause a change in the outcome. Qualitative estimates can incorporate knowledge that a short data history cannot capture, but they need documented assumptions. Simulation explores outcomes under specified distributions and rules; it does not make those specifications true.

Forecasts are useful for decisions such as staffing, inventory, liquidity, expense control, and capital investment. Comparing actual results with a forecast can identify variances and prompt investigation. It does not automatically reveal why a variance occurred. A useful forecast therefore includes both a number and an account of how that number was produced.

## Start with observations you can explain

Before fitting a model, define the target and the unit of analysis. Is the target monthly recognized revenue, cash receipts, or units sold? Are values recorded by transaction, location, or month? A time series needs a meaningful time index and consistent periods. A total that mixes currencies, accounting definitions, or reporting frequencies is not made comparable by averaging it.

Inspect the data for gaps, duplicates, unusual values, and changes in definition. A missing value is not necessarily zero. A missing month might reflect a data-export failure, a new reporting system, or a genuinely inactive operation. Each interpretation has different implications. Record how missing and invalid entries are handled so another analyst can reproduce the preparation.

Plots help reveal structure that a summary statistic can hide. A line chart can show trend, seasonality, abrupt breaks, or a one-time event. A distribution plot can reveal skew or outliers. When comparing actual and budgeted values, show the time period and scale clearly, and distinguish amounts from rates. A visually persuasive chart is not evidence that a model’s assumptions hold.

## Worked example: a bootstrap interval for mean cost

Suppose a small pilot records four weekly costs, in thousands of dollars: $8, 10, 11,$ and $15$. We want a descriptive estimate of the sample’s mean and an illustrative interval based on resampling these observations. This is not yet a forecast for next quarter, because the calculation does not model time, growth, seasonality, or a change in operations.

The sample mean is

$$
\bar{x} = \frac{8+10+11+15}{4} = \frac{44}{4} = 11.
$$

The median is the midpoint of the two middle sorted values, so it is $(10+11)/2=10.5$. The minimum is $8$ and the maximum is $15$. For the sample standard deviation, subtract the mean from each value and square the differences: $9, 1, 0,$ and $16$. Their sum is $26$. Divide by $n-1=3$, giving $26/3 \approx 8.667$, then take the square root: $s \approx 2.944$ thousand dollars.

A percentile bootstrap repeatedly draws four observations with replacement from the observed four values and calculates a mean for each resample. For example, resample $(8,10,10,15)$ has mean $43/4=10.75$. Resampling permits duplicates and can omit observed values. After many resamples, sort their means and take values near the 2.5th and 97.5th percentiles. Those endpoints form a 95% percentile bootstrap interval under the resampling procedure.

The interval is not a guarantee that future weekly cost will land within its endpoints. It quantifies sampling variability under a particular assumption: the observed values are a reasonable pool from which to resample. With four observations, that assumption deserves caution. If weeks are dependent, seasonal, or drawn from a changing process, ordinary resampling of individual values may not represent the data-generating structure. A time-aware method or more data may be needed.

## From descriptive summaries to forecasting workflows

Descriptive statistics are a sensible first checkpoint in a forecasting workflow. They can expose a wildly different scale, suspicious data cleaning, or a small effective sample. A reproducible summary also gives colleagues a quick way to inspect the input before a more complex model is built. But descriptive summaries do not predict future values by themselves.

After validating the data, choose a method that fits the question and data. A moving average can smooth short-term noise, but may lag a changing trend. Exponential smoothing gives more weight to recent observations according to its settings. ARIMA models represent patterns in a time series through autoregressive, differencing, and moving-average components. Regression can include relevant predictors, provided their timing and availability at forecast time are handled correctly.

Evaluate forecasts on data that was not used to fit them. For time-ordered data, a chronological holdout or rolling-origin evaluation usually better reflects real deployment than randomly splitting observations, because a random split can allow information from later periods to influence evaluation of earlier ones. Compare against a simple baseline, such as carrying forward the latest value or using a seasonal-naive estimate. Report an error measure that matches the decision. Absolute errors are in the target’s units; percentage errors can become unstable when actual values are near zero.

Scenario analysis adds a different kind of uncertainty. A base, upside, and downside case can make assumptions about prices, demand, or costs explicit. These are conditional cases, not probabilities unless probability distributions and a defensible method have been supplied. Monte Carlo simulation can propagate specified input variability through a model, but the resulting range depends on the input distributions, dependencies, and model structure. A simulation is an organized calculation, not a substitute for validating its assumptions.

## Put the analysis to work in budgets

A budget connects assumptions to planned resources. Separate inputs from calculations, label units and periods, and document where each assumption came from. A spreadsheet can make what-if analysis accessible: changing a sales growth assumption can show a corresponding revenue and staffing plan. Pivot tables can summarize actual costs by department or category. Charts can compare actuals and budget over time. These tools are most useful when the underlying definitions are consistent and formulas are auditable.

A practical budget process links forecast outputs to decisions without disguising judgment as precision. For example, a team might use a demand forecast to estimate required capacity, then create alternative plans for different demand levels. It should track actual outcomes against the assumptions that drove the plan. If actual spending exceeds budget, the variance is a signal to investigate, not proof of overspending or a model failure. A new supplier price, a delayed project, or a data error may each explain a difference.

Python can make repeated data preparation and analysis reproducible. A script can read a defined CSV, apply recorded cleaning rules, calculate summaries, and write structured output that can be checked into a workflow. Fixing a random seed makes a stochastic computation repeatable in the same implementation and environment. It does not remove uncertainty or prove that a method is appropriate. Record the input version, code version, parameters, and assumptions alongside results when the analysis informs decisions.

## Assumptions and common traps

Several practical limits deserve attention:

- **History may not represent the future.** Expansion, policy changes, inflation, product launches, and market shocks can break historical patterns.
- **Averages can hide important structure.** A mean across seasonal periods or distinct business units may describe no typical period or unit.
- **Missingness has meaning.** Dropping blank or invalid rows changes the analyzed sample. Report the exclusions and investigate their cause.
- **Precision is not accuracy.** A decimal-heavy output can still rest on sparse or noisy observations.
- **Intervals depend on method and assumptions.** A bootstrap interval, a model-based prediction interval, and a scenario range are not interchangeable.
- **Association is not causation.** A predictor that tracks revenue does not necessarily provide a lever that will increase revenue.
- **Reproducibility is not validity.** Repeating the same calculation makes it auditable, not automatically correct.

Use forecasts as decision support. Pair a point estimate with an explanation of assumptions, a sensible range where appropriate, and a plan for updating the analysis when new information arrives.

## Exercises with short answers

1. A monthly expense series has blank entries for two months. Should the analyst replace them with zero before calculating the average? **Answer:** Not without evidence that blank means no expense. Investigate and document the missingness first.

2. A team resamples four weekly costs and gets a narrow interval. Does that establish next quarter’s likely cost range? **Answer:** No. It describes uncertainty under the resampling setup and may fail to represent temporal dependence or future changes.

3. A model forecasts sales from advertising spend. Can the forecast alone show that increasing spend will cause sales to rise? **Answer:** No. Predictive association does not establish a causal effect.

4. Why keep a simple baseline when evaluating a sophisticated forecast? **Answer:** It shows whether the complex method improves on an understandable reference under the same evaluation design.

## The useful forecast is the one you can revisit

Financial forecasting connects observed data, assumptions, and decisions. Quantitative models can extract patterns, expert input can address sparse histories, and simulations can make specified uncertainty visible. Budgeting translates those estimates into plans, while actual-versus-forecast comparisons create opportunities to learn.

The discipline is not to choose the most elaborate tool. It is to keep the question clear, make the data and assumptions inspectable, validate the method against the task, and revise the forecast as conditions change. That is how a number becomes a useful part of a reproducible decision process.

---

Published 2026-10-20.

**Source:** Financial Data Analysis For FP&A- With Excel and Python, section **Chapter 5: Financial Forecasting and Budgeting**.

This is an original explanatory note; the source book is not redistributed.
