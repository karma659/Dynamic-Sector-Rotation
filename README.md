#Dynamic Sector Rotation with Machine Learning

*Executive Summary*

This report presents the implementation and performance evaluation of a dynamic sector rotation strategy applied to U.S. equity sector data. The strategy dynamically allocates capital across market sectors to maximize risk-adjusted returns, integrating machine learning (LightGBM), dimensionality reduction (PCA), clustering, and risk management techniques to capture persistent sector rotation patterns while maintaining robustness to market regime changes.

The back test covers the period 2014–2019 for in-sample (IS) testing and 2020–2025 for out-of-sample (OOS) validation, using sector ETF returns and the SPY ETF as a benchmark.

**Key performance results:**

|   | Annualized Return | Annual Volatility | Sharpe Ratio | Maximum Drawdown |
| :---: | ----- | ----- | ----- | ----- |
| **In-Sample (IS)** | **8.91%** | **7.96%** | **1.12** | **8.68%** |
| **Out of Sample (OOS)** | **8.76%** | **12.31%** | **0.71** | **19.64%** |
| **OSS-SPY** | **11.25%** | **10.00%** | **1.12** | **15.40%** |
| **OSS-Pairs Portfolio** | **7.35%** | **10.00%** | **0.74** | **16.11%** |

The results indicate that while the strategy maintained solid performance during IS, the OOS period showed increased volatility and a decline in the Sharpe ratio, suggesting sensitivity to shifting market regimes. Nonetheless, the LightGBM-based market-timing approach demonstrated resilience, outperforming the Pairs Portfolio in OOS returns while keeping drawdowns at moderate levels.

From a practical perspective, this study confirms the feasibility of combining machine learning with sector rotation for active portfolio management. It also highlights opportunities for further refinement through enhanced feature engineering, incorporation of transaction cost modeling, and regime detection mechanisms to strengthen real-world applicability.

*Introduction*

        	The financial markets are characterized by continuous shifts in economic cycles, investor sentiment, and sector performance. These dynamics create opportunities for sector rotation strategies, where capital is reallocated among different industries to capture relative strength while mitigating downside risk. Traditionally, such strategies have relied on macroeconomic indicators, moving averages, or momentum signals. However, the increasing availability of high-frequency, multi-dimensional data and advancements in machine learning have opened the door to more adaptive and data-driven approaches.

This project explores the application of Light Gradient Boosting Machine (LightGBM), a powerful gradient-boosting framework, in developing a Dynamic Sector Rotation Strategy. By combining dimensionality reduction techniques (PCA), unsupervised learning (clustering), and predictive modeling, the strategy aims to identify optimal sector allocations that outperform the market benchmark. In particular, the model forecasts short-term sector performance rankings and adjusts portfolio weights accordingly, incorporating risk management constraints to enhance risk-adjusted returns.

The motivation behind this study lies in bridging the gap between academic research and practical portfolio management. While machine learning-based asset allocation has shown promise in back testing, many approaches suffer from overfitting, lack of robustness in out-of-sample (OOS) periods, or poor adaptability to regime shifts. Our methodology addresses these issues by segmenting the dataset into in-sample (IS) and out-of-sample (OOS) periods for proper validation, applying feature engineering to extract meaningful signals from sector ETF price data, incorporating clustering methods to group similar market conditions and tailor predictions accordingly, and enforcing portfolio constraints to control volatility and drawdown.

*Data Description*

The dataset for this project consists of U.S. sector exchange-traded funds (ETFs) that represent key industries within the S\&P 500\.

| ETF | Industry |
| :---: | :---: |
| XLY | Consumer Discretionary |
| XLP | Consumer Staples |
| XLE | Energy |
| XLF | Financials |
| XLV | Health Care |
| XLI | Industrials |
| XLB | Materials |
| XLK | Technology |
| XLRE | Real Estate |
| XLU | Utilities |

These ETFs were chosen because they collectively cover the primary sectors tracked by the S\&P 500 index and provide a diversified representation of the U.S. equity market. Their historical price data captures sector-specific trends and cyclicality, which are essential for building a sector rotation strategy. The historical daily price data was obtained from a reputable financial data provider (e.g., Yahoo Finance). The dataset covers a 10-year period from January 2014 to December 2023, allowing the strategy to be tested across multiple market regimes, including bull markets, bear markets, and volatile transitional phases. To ensure the robustness of the model and avoid overfitting, the data was split into in sample (IS) and out of sample (OOS). In sample data is range from January 2014 to December 2019 that is model training and parameter tuning. Out of sample is range from January 2020 to December 2023 which is performance evaluation. This split ensures that the model is evaluated on entirely unseen market data, providing a realistic measure of predictive power and strategy viability.

| Table 1\. Summary statistics of sector ETFs (in-sample period) |
| ----- |


We summarized each sector ETF with risk or return descriptors computed on the training window: annualized return or volatility, downside volatility, Sharpe, Sortino, skewness, kurtosis, maximum drawdown, and beta versus SPY. Features were standardized and reduced to two principal components (PCA) before clustering (K-Means, k=4 via elbow). These features also served as inputs to LightGBM for the directional (up or down) next month return classification.

*Methodology*

        	The methodology for this project follows a systematic process designed to build and evaluate sector rotation strategies using U.S. sector ETFs. The workflow consists of feature engineering, dimensionality reduction, unsupervised clustering, supervised model training, and performance back testing. From the historical daily price series of each ETF, we computed a set of risk and return descriptors to serve as model features which are annualized return, volatility, downside volatility, penalizing negative returns, sharpe ratio, measuring risk-adjusted returns, sortino ratio, focusing on downside risk, skewness of returns distribution, kurtosis of returns distribution, maximum drawdown over the sample period, and beta relative to SPY. These features were computed separately for the in-sample (IS) period and standardized (mean \= 0, standard deviation \= 1\) to remove scale effects. These features were computed separately for the in-sample (IS) period and standardized (mean \= 0, standard deviation \= 1\) to remove scale effects.

        	To address potential multicollinearity among features and to capture the most significant patterns in the dataset, we applied Principal Component Analysis (PCA). The first two principal components were retained, explaining the majority of the variance in the feature space. This reduction simplifies the clustering step and improves computational efficiency while preserving essential information.



We applied K-Means clustering to the two PCA components to group ETFs with similar characteristics. The optimal number of clusters (k=4) was selected using the elbow method. Cluster assignments were later used as categorical features in the predictive model, representing different market regimes.

 

 


| :---- |

 

To predict the next month’s return direction (up \= 1, down \= 0), we trained a Light Gradient Boosting Machine (LightGBM) classifier. Inputs included PCA components, cluster labels, and lagged return features. The model was trained on the IS dataset and evaluated on the OOS dataset to assess predictive performance. Based on the LightGBM predictions, we constructed portfolios and measured performance across the IS and OOS periods. Evaluation metrics included cumulative return, annualized return, volatility, Sharpe ratio, and maximum drawdown (MDD). Performance was benchmarked against SPY to assess the strategy’s excess return potential.

*Performance Results*

        	The performance of the proposed sector rotation strategy was evaluated across both in-sample (IS) and out-of-sample (OOS) periods to assess robustness and predictive effectiveness. Benchmark comparison was made against the SPDR S\&P 500 ETF Trust (SPY). During the IS period (2014–2019), the strategy achieved an annualized return of 8.91%, annual volatility of 7.96%, and a Sharpe ratio of 1.12, with a maximum drawdown of 8.68%. This indicates strong risk-adjusted returns and relatively low drawdowns. In comparison, the OOS period (2020–2025) yielded an annualized return of 8.76% with higher volatility 12.31% and a reduced Sharpe ratio 0.71, suggesting that the strategy’s performance was more sensitive to market regime changes post-2020.

![A table with numbers and symbolsAI-generated content may be incorrect.][image7]

        	Despite the drop in Sharpe ratio during OOS, the LightGBM-based market-timing approach outperformed the Pairs Portfolio benchmark in OOS returns (8.76% vs. 7.35%) while keeping maximum drawdowns at a moderate level (19.64% vs. 16.11%).

![A graph with lines and numbersAI-generated content may be incorrect.][image8]

Moreover, when compared directly to SPY in the OOS period, the strategy’s excess return potential is showing more stable performance despite SPY’s strong upward trend.

![A graph of different colored linesAI-generated content may be incorrect.][image9]

![A table of numbers with textAI-generated content may be incorrect.][image10]

        	This section presents the performance evaluation of the two proposed strategies—LGBM Market–Timing and Pairs Portfolio—benchmarked against the SPY ETF. The evaluation is conducted over both the in-sample (IS) period (2014–2019) and the out-of-sample (OOS) period (2020–2025). Key metrics include annualized return (AnnRet), annualized volatility (AnnVol), Sharpe ratio, and maximum drawdown (MDD). During the IS period, LGBM Market–Timing achieved an annualized return of 8.91%, paired with the lowest volatility of 7.96% among the three strategies. This resulted in the highest Sharpe ratio 1.12, indicating superior risk-adjusted returns.

The Pairs Portfolio recorded a slightly higher return of 9.94% but came with higher volatility 10.00% and a lower Sharpe ratio 0.99. SPY delivered the highest absolute return 10.48% but had moderately higher volatility 10.00% and a Sharpe ratio 1.05 slightly lower than LGBM.

Overall, LGBM outperformed in risk-adjusted terms, while SPY maintained an edge in absolute returns.

In the OOS period, SPY significantly outperformed both active strategies, generating an annualized return of 11.25% with a Sharpe ratio of 1.12. LGBM Market–Timing delivered a positive return of 8.76%, but volatility rose to 12.31%, reducing the Sharpe ratio to 0.71. Its maximum drawdown widened to 19.64%, indicating higher downside risk in real-time conditions. The Pairs Portfolio underperformed both, posting a return of 7.35% and a Sharpe ratio of 0.74, although it achieved a smaller MDD (16.11%) compared to LGBM. Overall, SPY dominated in both absolute and risk-adjusted performance, reflecting the difficulty of outperforming a passive benchmark during a sustained bull market.

LGBM Market Timing demonstrated strong IS performance, particularly in risk-adjusted terms, and remained competitive OOS but suffered from increased volatility and drawdowns.

Pairs Portfolio delivered reasonable IS performance but lagged in both return and Sharpe ratio OOS, despite slightly better drawdown control than LGBM.

SPY Benchmark outperformed in OOS, highlighting the resilience of a passive buy-and-hold strategy in upward-trending markets. These findings suggest that while the LGBM approach shows predictive value, further refinement is needed to improve volatility management and adaptability to different market regimes.

*Conclusion*

This study evaluated a sector rotation strategy using a LightGBM-based market-timing approach and compared it with the Pairs Portfolio benchmark and the SPDR S\&P 500 ETF Trust (SPY). The analysis covered both the in-sample (2014–2019) and out-of-sample (2020–2025) periods to examine the strategy’s robustness and adaptability. The LightGBM strategy achieved the highest risk-adjusted return in the in-sample period, with a Sharpe ratio of 1.12 and the lowest volatility among the tested strategies, highlighting its strength in stable or moderately volatile environments. In the out-of-sample results, the strategy outperformed the Pairs Portfolio in absolute returns (8.76% vs. 7.35%) but lagged behind SPY in both returns and Sharpe ratio under prolonged bull market conditions. The higher volatility (12.31%) and maximum drawdown (19.64%) suggest that the strategy’s performance was more sensitive to market regime changes in strongly trending markets.

From an investor’s perspective, this strategy may appeal to those who prefer a more dynamic, model-driven approach that adapts to changing sector conditions rather than relying solely on a passive buy-and-hold strategy. However, in a sustained bull market, a passive benchmark such as SPY may still be more advantageous in terms of absolute returns.
