# options-pricer

[![CI](https://github.com/JanBoend/options-pricer/actions/workflows/ci.yml/badge.svg)](https://github.com/JanBoend/options-pricer/actions/workflows/ci.yml)
![Python](https://img.shields.io/badge/python-3.10+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

Black-Scholes pricer with Monte Carlo as a cross-check, full Greeks, an implied vol solver, and a small web UI on top. Built this to actually understand where closed-form pricing breaks down versus where you need simulation.

See also: [quant-engine](https://github.com/JanBoend/quant-engine), [market-regime-detector](https://github.com/JanBoend/market-regime-detector), [factor-backtest](https://github.com/JanBoend/factor-backtest).

## Prices

- European options — Black-Scholes closed-form, Monte Carlo as a sanity check
- Asian options — Monte Carlo, arithmetic average payoff
- Barrier options — down-and-in, Monte Carlo

## Greeks

| Greek | Meaning |
|---|---|
| Delta | Price sensitivity to $1 move in spot |
| Gamma | Delta sensitivity to $1 move in spot |
| Theta | Time decay per calendar day |
| Vega  | Price sensitivity to 1% move in vol |
| Rho   | Price sensitivity to 1% move in risk-free rate |

## Run it

```bash
pip install -r requirements.txt
python app.py   # http://localhost:5002
```

```python
from pricer.black_scholes import all_greeks

result = all_greeks(S=100, K=100, T=30/365, r=0.05, sigma=0.20, option_type="call")
# {'price': 2.79, 'delta': 0.527, 'gamma': 0.0635, 'theta': -0.0176, 'vega': 0.116, 'rho': 0.012}
```

```python
from pricer.implied_vol import implied_vol

iv = implied_vol(market_price=3.50, S=100, K=100, T=30/365, r=0.05)
print(f"IV: {iv:.1%}")  # IV: 27.4%
```

Monte Carlo converges to the Black-Scholes price for European options — checked in the test suite. It's needed for the path-dependent ones, since Asian and barrier options don't have a closed-form solution here.

## Tests

```bash
pytest tests/ -v
```

8 tests: call/put against known reference values, put-call parity, Greeks bounds, deep ITM delta behavior.
