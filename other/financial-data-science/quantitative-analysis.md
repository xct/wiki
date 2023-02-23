# Quantitative Analysis

### Normalization 

When having missing data, especially in the beginning, first backfill and then divide each price by its initial value. The resulting values will now show the gains in % on the y-axis.

### Adjusted Close Price

This is used to factor in stock splits & dividends - use the adjusted close for calculating things like returns.

### Comparing stock prices

A good way to compare prices of different magnitudes is to subtract the mean and divide by standard deviation:

```text
close_prices - (close_prices.mean())/close_prices.std()
```

### Skewness

### Confidence Interval

Distribution leans to a side \(positive left, negative right\). Positive is what we want.

Describes how good the sample mean represents the data. It can be for example the bell curve of a normal distribution without the tails \(+-5%\).

### Kurtosis

Distribution compressed or taller \("heavy" tails\). We want low Kurtosis \(less extreme values\).

