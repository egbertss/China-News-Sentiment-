# Can AI-read news predict Chinese stock returns?

I built this pipeline to test whether a large language model reading news headlines can help predict the Shanghai Composite. I chose the market, the data sources, the model, the tests and the code design myself.

## What it does

1. Collects China news headlines from the New York Times Archive API, the Guardian Open Platform and Google News, February 2024 to November 2026.
2. Scores each headline UP (+1), DOWN (−1) or UNCERTAIN (0) with Gemini, zero-shot, using the prompt from Ma, Lyu and Li (2024).
3. Averages the scores by week and compares them with the weekly equity risk premium: the Shanghai Composite log return minus the China 1-year government yield.
4. Tests the score in the same week, one week ahead, and on top of the NBS manufacturing PMI, with Newey-West errors, an expanding-window out-of-sample test and a Clark-West test.
5. Checks robustness across sources and topics, and compares the single prompt with a bull, bear and judge design where two models argue and a third scores.

## Results

| Test | Result |
|---|---|
| Headlines scored | 6,598 over 145 weeks |
| Same week | t = 2.32 (Newey-West 1.68) |
| Same week, without headlines that report market moves | t = 1.46 |
| Next week | t = −1.34 |
| PMI + news, out of sample | 8.1% worse than forecasting the historical average |

The news score moves with Chinese shares in the same week, largely because some headlines report price moves that have already happened. It does not help predict the following week, on its own or on top of the PMI.

## Files

- `china_news_sentiment.ipynb`: the full pipeline, written for Google Colab.
- `requirements.txt`: Python packages.

## How to run it

1. Open the notebook in Google Colab.
2. Add three Colab Secrets: `GEMINI_API_KEY`, `NYT_KEY` and `GUARDIAN_KEY`. No keys are stored in the code.
3. Run all cells. Colab asks for an input workbook with daily index prices, a daily risk-free rate and the monthly PMI. That workbook comes from LSEG and is not included for licensing reasons. If those sheets are empty, the notebook falls back to free index data from Yahoo Finance and the 3-month SHIBOR, and skips the PMI models.

A full run takes about 40 to 50 minutes on the Gemini free tier. The debate, bull, bear and judge, and kappa cells are switched off by default.

## Limitations

The model may have seen news from the sample period during training. Google News gives dates but not times. The headlines are in English, not Chinese. Headlines are scored 50 per request to stay within the free quota.

## Reference

Ma, Lyu and Li (2024). Can ChatGPT predict Chinese equity premiums? Finance Research Letters.
