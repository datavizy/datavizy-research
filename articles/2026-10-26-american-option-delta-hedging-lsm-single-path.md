# A Hedge Is a Model Under Stress: Estimating American-Option Delta Along One Path

Suppose an American put is worth $8.40, and the underlying asset rises by a small amount. How much should the put’s estimated value change? That question sounds like a derivative calculation, but an American option complicates it: its value depends not only on the current asset price, but also on the possibility of exercising later. In a simulation, the challenge is to estimate that value consistently at each time step, then see how a hedge performs along realized paths.

The section considered here builds that experiment around least-squares Monte Carlo (LSM) valuation in a BCC97 setting. Its central move is practical: estimate delta by repricing at two nearby asset levels, and use regression to obtain option values at intermediate states. Then rebalance a stock-and-bond portfolio along each simulated path and compare it with the option’s value. The result is a distribution of hedging profit and loss, not a proof that the hedge is perfect.

## From a value function to a hedge ratio

For an option value $V(S, x, t)$, where $S$ is the underlying price and $x$ collects other state variables, delta is the sensitivity to $S$ while the other inputs are held fixed:

$$
\Delta = \frac{\partial V}{\partial S}.
$$

A numerical first-order estimate uses a central finite difference:

$$
\widehat{\Delta} = \frac{V(S+h, x, t) - V(S-h, x, t)}{2h}.
$$

The code sets the relative bump to `dis = 0.05`, and at the initial time sets $ds = dis \cdot S_0$. Its two initial valuation points are $S_0 + (2-a)ds$ and $S_0 - ads$. Their difference is exactly $2ds$, because $(2-a)ds - (-a ds) = 2ds$. Thus the initial delta estimate uses a centered interval around $S_0 + (1-a)ds$, not necessarily around $S_0$ unless $a=1$. With the default $a=1$, the points are $S_0+ds$ and $S_0-ds$, so the estimate is centered at $S_0$.

At later times, the code uses the same asymmetric parameterization around the current $S[t,p]$: the two bumped values are $S+(2-a)ds$ and $S-ads$. Their midpoint is $S+(1-a)ds$. Again, the default $a=1$ gives the familiar central difference. The code comment says $a$ is in $[0,2]$; interpreting the result as a derivative at the current state requires the centered choice, or a sufficiently small bump that any offset is negligible. The bump is a numerical choice, not a universal constant.

## Where LSM enters the calculation

American exercise means that the holder can choose when to receive the payoff. In LSM, simulated paths supply examples of future outcomes, and a regression approximates continuation value as a function of the current state. At each time step, that estimate helps determine whether continuing is preferable to exercising. The first call to `BCC97_lsm_put_value` assigns its returned state arrays, exercise indicators, and regression coefficients to the hedge routine; it is the upper-bump valuation call. The excerpt does not show a separate hedge-path set initialized at $S_0$ or a holdout split.

For a bumped state at time $t$, the hedging code assembles a basis of terms involving asset price $S$, volatility state $v$, and rate state $r$. The basis includes a constant, the individual state variables and their squares, and selected products such as $Sv$, $Sr$, and $vr$. It takes a dot product of the regression coefficients `rg[t]` with that basis to estimate continuation value. It then applies `max(0, estimate)`, preventing a negative estimated put value in this calculation. This floor is a modeling safeguard, not a guarantee that the regression is accurate.

The code’s approach separates two tasks. LSM estimates an option value function from simulated paths. A finite difference of that estimated function supplies the hedge ratio. The accuracy of the resulting delta therefore depends on the regression, simulation noise, and the bump. At initialization, the upper- and lower-bump values come from separate calls to `BCC97_lsm_put_value`; the excerpt does not show common random numbers, so their difference may include Monte Carlo noise. A noisy regression can make nearby estimates jump around; an excessively large bump can blur local curvature, while an excessively small one can magnify numerical noise.

## A small worked example, with the arithmetic exposed

Imagine the default centered bump at a particular state is $ds=2$. Suppose the regression produces estimated put values of $12.60$ at $S+2$ and $11.84$ at $S-2$. Then:

$$
\widehat{\Delta} = \frac{12.60-11.84}{2(2)} = \frac{0.76}{4}=0.19.
$$

The estimated option value rises by about $0.19$ for a one-unit rise in the underlying, locally and with the other state variables held fixed. For a short position in one put, a simple delta hedge would hold $0.19$ units of the underlying. The hedge routine instead starts with the long-option value $V_0$ and sets the bond position to $bo=V_0-\Delta_0S_0$. The stock value plus bond value then equals the initial option value: $\Delta_0S_0+bo=V_0$.

For example, if $V_0=8.40$, $S_0=100$, and $\Delta_0=0.19$, the initial bond position is $8.40-(0.19)(100)=-10.60$. The negative amount means borrowing in the bond account. Check: $19.00-10.60=8.40$. This is a replication portfolio for the long option under the model’s accounting convention. The P&L calculation later compares portfolio value with the option-related quantity `h[t,p]`, with a bond adjustment through `B(alpha_t)`. Interpreting that final number precisely requires the definitions and units in the supporting valuation code, which are not included in this section alone.

## Following one path, repeating the experiment

The valuation call uses $M$ time steps over horizon $T$. The hedging routine uses the module-level `dt`, initialized as $T/M$ before the function; passing a different $M$ does not update that value and can make the hedge step size inconsistent. At each path and time, it carries a stock position and a bond balance. When the option has not exercised, the code accrues the bond using an exponential discount factor based on the average of adjacent short-rate observations:

$$
\exp\left(\frac{r_t+r_{t-1}}{2}dt\right).
$$

At a nonterminal time, it values the portfolio, bumps the asset state in both directions, evaluates the regression, and updates delta. At maturity it sets delta to zero after valuing the portfolio. If the exercise indicator `ex[t,p]` is nonzero, the loop records the portfolio value and stops rebalancing on that path. This is why the exercise decision matters operationally: a hedge for a live option is not automatically continued after exercise.

The function is described as a single-path hedging exercise, but its outer loop repeats that pathwise procedure over up to `min(I, 10000)` path indices. Here `I` is passed to the valuation routine and also limits the number of dynamic replications. The code reports maximum, average, median, and minimum P&L across those replications. Those summaries describe the simulated outcomes; they do not establish statistical significance, guarantee coverage of rare losses, or demonstrate performance outside the assumed model.

The source section notes a concern about in-sample bias and points toward using distinct simulation data for valuation and hedge testing. The supplied excerpt shows valuation calls and path indexing, but does not fully document how paths are separated internally. Therefore, one should inspect the supporting routine before claiming that the reported hedging paths are out of sample. Reusing training paths can make a strategy look better than it performs on fresh paths.

## What this experiment is useful for

A pathwise hedge experiment is useful as a diagnostic. It can reveal whether errors cluster near exercise, whether the hedge becomes unstable in particular volatility or rate states, and how changing the number of time steps affects P&L dispersion. In a jump-capable model such as BCC97, a continuous rebalancing argument does not erase jump risk: the underlying can move between hedge dates, leaving a residual mismatch. Discrete rebalancing, estimated deltas, model assumptions, and transaction costs can all matter in practice.

Researchers can use the workflow to compare bump sizes, regression bases, and out-of-sample designs. Developers can log each state, delta, exercise indicator, and hedge adjustment, then test invariants such as the initial portfolio identity. A histogram or quantile plot of P&L often reveals more than four extrema and central summaries, especially when tails are asymmetric. That plot should be labeled as a simulation result under specified assumptions, not as a forecast of real-world loss.

## Assumptions and common traps

The finite-difference estimate assumes the value function is locally smooth enough for a first-order sensitivity to be meaningful. American exercise can create a boundary where small state changes alter the exercise decision, making the estimate sensitive to regression error. Regression basis choice is consequential: too little flexibility can bias continuation estimates; too much can overfit simulated data.

The routine uses a fixed proportional bump, so the absolute bump changes with the asset level. This can be sensible for scale, but it does not ensure a good derivative estimate everywhere. The code also clips regression values below zero, while the true American put payoff and continuation value are nonnegative. Clipping avoids an impossible negative estimate but can introduce its own bias near zero.

Finally, the excerpt is code, not a complete specification of the model. It imports valuation functions, model parameters, and the bond function from elsewhere. It does not show transaction costs, market calibration, data fitting, uncertainty intervals, or all conventions behind `h`. Avoid reading the P&L summaries as causal evidence or as a statistical test. They are descriptive outcomes conditional on the implementation and simulated paths.

## Exercises with short answers

1. If the bumped values are $7.10$ at $S+1.5$ and $7.70$ at $S-1.5$, what is the centered delta? **Answer:** $(7.10-7.70)/3=-0.20$.
2. Why can a smaller bump make an estimate worse? **Answer:** It may amplify regression or floating-point noise in the difference.
3. What should you check before calling hedge P&L out of sample? **Answer:** Confirm that paths used to fit the exercise and continuation estimates are distinct from paths used to assess hedging.

The useful lesson is not that one finite difference solves American-option hedging. It is that a hedge ratio is an estimated quantity embedded in a larger computational pipeline. Making the bump, regression, exercise rule, path design, and accounting visible turns a compact simulation into an experiment that can be tested and improved.

---

Published 2026-10-26.

**Source:** Derivatives Analytics with Python- Data Analysis, Models, section **13.5.4 LSM Delta Hedging in BCC97 (Single Path)**.

This is an original explanatory note; the source book is not redistributed.
