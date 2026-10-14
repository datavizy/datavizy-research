# Reading the Surface: What Black–Scholes–Merton Call Greeks Tell You

A risk manager opens a dashboard and sees a call option’s delta, gamma, theta, vega, and rho. The values look precise, but what do they actually say? More importantly, how should they change when the stock price, strike, or maturity changes? A single number is a local summary. A surface of numbers can reveal how that summary responds across possible conditions.

The selected section of *Derivatives Analytics with Python* implements the five standard Greeks for a European call under the Black–Scholes–Merton (BSM) model, then evaluates a chosen Greek across strike and maturity to make a wireframe plot. The formulas are familiar, but the useful lesson is broader: model outputs become easier to inspect when we connect definitions, units, assumptions, and visual design.

## Start with time to maturity

Let $S$ be the underlying asset price at valuation time $t$, $K$ the strike, $T$ the maturity time, $r$ a constant risk-free rate, and $\sigma$ the volatility. Define the remaining time as $\tau = T-t$. For a standard BSM call with no dividends, the familiar intermediate quantities are

$$
d_1 = \frac{\ln(S/K) + (r + \tfrac{1}{2}\sigma^2)\tau}{\sigma\sqrt{\tau}},
\qquad
d_2 = d_1 - \sigma\sqrt{\tau}.
$$

Write $N(x)$ for the standard normal cumulative distribution function and $\phi(x)$ for its probability density function. The section delegates calculation of $d_1$, $N$, and the density to functions imported from a valuation module. The definitions above explain their standard BSM meaning; the excerpt itself does not show the imported implementations.

For a call, the five quantities are

$$
\Delta = N(d_1), \qquad
\Gamma = \frac{\phi(d_1)}{S\sigma\sqrt{\tau}},
$$

$$
\Theta = -\frac{S\phi(d_1)\sigma}{2\sqrt{\tau}} - rK e^{-r\tau}N(d_2),
$$

$$
\rho = K\tau e^{-r\tau}N(d_2), \qquad
\text{Vega} = S\phi(d_1)\sqrt{\tau}.
$$

These are sensitivities of the model price, not predictions of realized market changes. Delta and the other Greeks are partial derivatives: they describe how the price changes for a small change in one input while the other inputs are held fixed. The formulas use a particular model and assumptions, so a Greek is not a guarantee about how a traded option will behave.

## Five views of one option

**Delta** is the derivative of call value with respect to the underlying price. For a standard call in this model, it lies between zero and one. A delta of $0.64$ suggests that, near the current inputs, a one-unit increase in the underlying corresponds to about a $0.64$ increase in the option value, with other inputs fixed. This is a local approximation, not a fixed hedge ratio for all prices.

**Gamma** is the derivative of delta with respect to the underlying price. It describes how quickly delta changes as the underlying moves. Positive gamma for a standard BSM call means delta rises with the underlying price. Gamma can become especially important near expiry, when small underlying moves may cause a large change in delta around the strike.

**Theta** is the derivative with respect to the passage of calendar time under a common convention. In the formula above, it is the change in model value per year as $t$ advances and $T$ remains fixed. That convention explains the negative sign on the diffusion term. Theta is often negative for a call, but its value depends on the inputs; do not infer a universal sign from the label alone.

**Vega** is the derivative with respect to volatility, where volatility is expressed as a decimal. A vega of $37.5$ means an approximate $0.375$ price change for a one-percentage-point volatility increase, because one percentage point is $0.01$. Some systems report vega per volatility point instead, so check the convention before comparing values.

**Rho** measures sensitivity to the risk-free rate. In the formula, the rate is a decimal and rho is per one-unit rate change. A one-percentage-point increase therefore corresponds approximately to $0.01\rho$, not the full rho. Rate conventions and day-count choices matter when applying this to actual instruments.

## A checked numerical example

Consider an at-the-money call with $S=100$, $K=100$, $\tau=1$ year, $r=0.05$, and $\sigma=0.20$. Then

$$
d_1 = \frac{\ln(1)+(0.05+0.5(0.20)^2)(1)}{0.20} = \frac{0.07}{0.20} = 0.35,
\qquad
d_2 = 0.35-0.20=0.15.
$$

Using $N(0.35)\approx0.636831$, $N(0.15)\approx0.559618$, and $\phi(0.35)\approx0.375240$, we obtain:

- $\Delta \approx 0.636831$.
- $\Gamma \approx 0.375240/(100\cdot0.20\cdot1)=0.018762$ per underlying-price unit.
- $\Theta \approx -[100\cdot0.375240\cdot0.20/2 + 0.05\cdot100\cdot e^{-0.05}\cdot0.559618] \approx -(3.752404+2.6616) \approx -6.414$ per year.
- $\rho \approx 100\cdot1\cdot e^{-0.05}\cdot0.559618 \approx 53.233$ per unit rate, or about $0.532$ for a one-percentage-point rate rise.
- $\text{Vega} \approx 100\cdot0.375240\cdot1=37.524$ per unit volatility, or about $0.375$ for a one-percentage-point volatility rise.

The arithmetic also clarifies units. Delta is a price ratio, gamma changes delta per underlying-price unit, and vega and rho need scaling when an input moves by one percentage point. The values are local derivatives at the stated inputs, not exact price changes for large shifts.

## Turn a formula into a surface

A plot of one Greek over strike and maturity adds context that a single quote cannot provide. The source script fixes $S=100$, $t=0$, $r=0.05$, and $\sigma=0.2$. It evaluates the chosen function at 25 maturities from $0.01$ to $1$ and 25 strikes from $80$ to $120$, producing $25\times25=625$ values. The resulting wireframe uses strike $K$ and maturity $T$ as horizontal axes, with the Greek as height.

This design makes comparisons visible. For instance, a trader can inspect how gamma changes around the strike as maturity shortens, or how vega varies across strikes and expiries. A researcher can use such a grid to check whether outputs behave plausibly before building them into a larger analysis. The plot is a diagnostic view of the model, not evidence that its assumptions fit observed prices.

The excerpt’s plotting function computes a surface for whichever Greek function it receives. It does not specify an observed-data comparison, uncertainty interval, or calibration procedure. It also begins the maturity grid at $0.01$, rather than zero, which avoids directly evaluating formulas containing $\sqrt{\tau}$ in the denominator at expiry. Near expiry, however, the values can still change sharply, so grid resolution and axis scaling affect what the wireframe reveals.

## Assumptions and implementation checks

The displayed formulas describe a European call in the standard BSM setting, with constant volatility and a constant risk-free rate over the option’s life, and no dividend yield in the formulas shown. They assume an underlying price above zero, positive volatility, and positive remaining time. The section’s functions accept $t$ and $T$ separately, but their formulas depend on the difference $T-t$.

Those assumptions limit interpretation. A dividend-paying underlying requires an appropriate model adjustment. Early exercise is outside the scope of a European-option formula. Real markets can have volatility and rates that vary with strike and maturity, while this plotting example holds them fixed. And a model Greek is not automatically a risk measure for a portfolio unless positions, units, and aggregation are handled consistently.

Several implementation details deserve attention when adapting the code. Validate $T>t$, $S>0$, $K>0$, and $\sigma>0$ before calculation. The source example does not show such validation. Its grid uses a historical NumPy alias, `np.float`, which may be unavailable in newer NumPy versions; a maintained implementation should select a supported floating-point dtype. Check that array orientation matches the meshgrid axes, and label units and parameter values on the plot. Finally, test computed values against finite differences or a trusted independent implementation, taking care that finite-difference step sizes are appropriate.

## When Greek surfaces are useful

A Greek surface can support several practical tasks. Risk teams can compare sensitivity across a range of strikes and expiries rather than focusing on one contract. Developers can spot unexpected discontinuities or incorrect axis ordering. Educators can show why a Greek is local and why its value changes as market conditions change. Analysts can compare scenarios while clearly separating fixed parameters from the variable axes.

For reproducibility, record the model assumptions, parameter values, units, software versions, and grid construction alongside the visualization. That record helps another person recreate the same surface and distinguish a change in model behavior from a change in plotting or input configuration. A useful plot is not just a picture. It is a compact report of a computational experiment.

## Exercises with short answers

1. If vega is $24$ per unit volatility, what is the local value change for a $0.5$ percentage-point increase in volatility? **Answer:** $24\times0.005=0.12$.
2. Why does the example avoid zero maturity? **Answer:** The formulas contain $\sqrt{\tau}$ in denominators, so at $\tau=0$ they are undefined or singular.
3. If a plotted surface changes when you alter the fixed rate, what should you record? **Answer:** The rate and its convention, along with the remaining model parameters and grid, so the comparison is reproducible.

The main habit is simple: read each Greek as a model-based local derivative, check its units, and examine how it behaves across the conditions that matter. A well-designed surface makes that last step visible without pretending that a model is the market.

---

Published 2026-10-14.

**Source:** Derivatives Analytics with Python- Data Analysis, Models, section **5.6.3 Script for BSM Call Greeks**.

This is an original explanatory note; the source book is not redistributed.
