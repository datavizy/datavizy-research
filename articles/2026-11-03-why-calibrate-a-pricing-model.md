# Why Calibrate a Pricing Model? From Many Possible Prices to Market Fit

A model can fit the rules of no-arbitrage and still leave a derivative’s price undetermined. That sounds like a contradiction until we ask a sharper question: do the assets available for trading span every possible future payoff? If not, more than one pricing rule can agree with today’s traded prices. Calibration is one way to bring market information to bear on that choice.

## No-arbitrage does not always give one price

In a one-period market, a portfolio of traded assets generates a collection of possible payoffs. A claim is replicable when its payoff belongs to that collection: some portfolio matches it in every state. If it is replicable, its price must equal the cost of the matching portfolio, or an arbitrage would be available. If all claims can be replicated, the market is complete, and this logic pins down their prices.

When a claim cannot be replicated, absence of arbitrage still constrains its price, but may not fix a single value. The mathematical counterpart is that there can be several risk-neutral probability measures, each consistent with observed asset prices. A derivative’s price, computed as a discounted expected payoff under one such measure, can then depend on which measure is selected.

The distinction between two senses of incompleteness matters in models with jumps. In the passage discussed here, the continuous-process setting uses “incomplete” when a claim cannot be replicated using its underlying assets. The wider sense, relevant to jump processes, asks whether replication remains impossible even when all available derivative assets may also be traded. The section characterizes the Black–Scholes–Merton model as complete, the Heston model as incomplete in the narrow sense but complete in the wider sense, and the Merton jump-diffusion model as incomplete in the wider sense because stochastic jump sizes require more hedging instruments than are practically or theoretically available. It consequently describes the Bates model as incomplete in that wider sense. These are the section’s model-level claims, not a general promise that a real market is complete whenever a particular model says so.

## A small market with two valid prices

Here is a self-contained example. It is deliberately different from the section’s example. There are two dates, today and tomorrow, and three equally likely states tomorrow. A bond costs $1 today and pays $1 in every state. A stock costs $1 today and pays, respectively, $0, $1, or $2 in the three states. The interest rate is zero.

A portfolio holding $b$ bonds and $s$ shares costs $b+s$ today and pays $(b,\,b+s,\,b+2s)$ across the three states. Consider a claim paying $(0,\,0,\,1)$. Replication would require

$$
b=0,\qquad b+s=0,\qquad b+2s=1.
$$

The first equation gives $b=0$, and the second then gives $s=0$. Those values make the third equation read $0=1$. No portfolio of the available assets replicates the claim. The market is incomplete.

Now find risk-neutral probabilities $(q_1,q_2,q_3)$. They must be nonnegative and sum to one. Since the stock’s price is $1$ and the bond’s price is $1$, its expected payoff under these probabilities must also be $1$:

$$
q_1+q_2+q_3=1,\qquad 0q_1+1q_2+2q_3=1.
$$

Subtracting the first equation from the second gives $q_3=q_1$. Setting $q_1=q_3=t$ gives $q_2=1-2t$. Strictly positive probabilities require $0<t<1/2$. Every such choice prices the bond and stock correctly, so each is consistent with no-arbitrage in this finite model.

The claim’s discounted expected payoff is $q_3$, because the rate is zero and the claim pays one only in state three. Thus its price can be any value $t$ in the open interval $(0,1/2)$ under these strictly positive measures. For example, $t=1/4$ gives probabilities $(1/4,1/2,1/4)$ and claim price $1/4$. The different choice $t=1/3$ gives $(1/3,1/3,1/3)$ and price $1/3$. Both probability vectors sum to one, both give expected stock payoff $0q_1+q_2+2q_3=1$, and both therefore preserve the observed prices. No-arbitrage has bounded the price without selecting one unique value.

## Calibration asks the market for constraints

Calibration means selecting model parameters so model prices agree with selected market prices, often prices of liquid, plain-vanilla options. Those instruments add information beyond the underlying asset price. In effect, their prices constrain the model’s risk-neutral dynamics and help select a market-consistent pricing rule within the model’s allowed family.

This does not mean a calibration magically completes the market. It means a practitioner chooses a model class and uses observed prices to identify parameters, or a pricing measure within that class, according to a stated fitting procedure. A model may fit a chosen set of option prices while still leaving uncertainty about untraded or illiquid claims. Choices of instruments, weights, parameter restrictions, and objective function matter. So do data quality and the model’s ability to represent the market.

The section’s practical argument is that calibration supplies the market’s choice where no simple theoretical criterion uniquely chooses a risk-neutral measure. It says the desired outcome is correct pricing of liquid plain-vanilla options and unique prices for other derivatives that remain consistent with no-arbitrage. Read this as a modeling objective, not a guarantee that every calibration procedure yields a uniquely identified parameter set or an objectively true price. Multiple parameter values can fit observed quotes nearly equally well, and an exotic claim’s value remains exposed to model assumptions.

## A useful workflow, and what it cannot settle

A disciplined calibration workflow starts with data. Record which instruments and quote conventions are included, the observation time, and any filters for stale or unreliable quotes. Choose a model and parameterization, then define a loss function, such as weighted squared differences between model prices and market prices. State the weights: an unweighted error gives equal influence to each quote, while a weighted error can emphasize more reliable observations. Next, fit parameters subject to economically and mathematically valid bounds. Finally, inspect residuals, test sensitivity to the instrument set and weights, and document the calibrated parameters and data snapshot.

Calibration is useful for pricing and risk analysis, but it is not a substitute for validation. A low fitting error on calibration instruments says little by itself about performance on other dates or products. Compare prices with independent observations where possible, examine hedge behavior, and report uncertainty or sensitivity. Under jump risk, in particular, a model-based hedge may reduce exposure without eliminating it. The section emphasizes that hedging in an incomplete market becomes an approximation problem, not perfect replication.

## Pitfalls worth catching early

A common mistake is to treat a calibrated price as uniquely dictated by no-arbitrage. The example shows why that is false in an incomplete market. Another is to confuse a numerical optimizer’s single output with proof of a unique solution. Optimization may return one parameter vector because of its starting point or constraints, even when other vectors fit almost as well.

Also keep market price, model price, and hedge value distinct. A quoted option price is an observation with conventions and potential noise. A model price is an implication of specified dynamics and parameters. A hedge value depends on trading instruments and rebalancing assumptions. Calibration connects the first two; it does not erase the assumptions behind the third.

## Exercises with short answers

1. In the worked market, why can’t the claim $(0,0,1)$ be replicated? **Answer:** The first two state equations force $b=s=0$, which fails in state three.
2. What range of claim prices follows from strictly positive risk-neutral probabilities? **Answer:** $0<t<1/2$, since the price is $q_3=t$ and $q_2=1-2t>0$.
3. If a model matches every option quote in a calibration set, has it proved that its price for an untraded exotic is correct? **Answer:** No. The result remains conditional on the model, the calibration data, and the selected fitting method.

Calibration is valuable precisely because no-arbitrage alone can leave room for choice. A sound analysis makes that choice explicit, ties it to observed prices, and keeps the remaining uncertainty visible.

---

Published 2026-11-03.

**Source:** Derivatives Analytics with Python- Data Analysis, Models, section **11.2.1 Why Calibration at All?**.

This is an original explanatory note; the source book is not redistributed.
