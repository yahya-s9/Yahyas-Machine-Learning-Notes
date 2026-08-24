- descriptive statistics, summarize and understand data at a glance, extract a few key metrics.
	- center of ur data: mean, median, mode
	- variance
	- how far spread out is ur data: standard deviation
- will need to understand probability distributions, and build intuition for Bayesian inference to batch normalization

- Mean (average)
	- single value that represents the typical data point
	- ![[Screenshot 2026-02-07 at 2.58.51 PM.png]]
	- arithmetic mean = sum of all values / count of values.

- Median (middle value)
	- mean works well on symmetric data but outliers can pull it away from center
	- median solves it by focusing on position than value.
	- median = middle value when all data points sorted. if even number of points, average of two middle values.
	- ![[Screenshot 2026-02-07 at 3.07.22 PM.png]]

- Mode (most frequent)
	- ![[Screenshot 2026-02-07 at 3.08.31 PM.png]]
	- multimodal = multiple frequent values
	- handy with categorical data

- Skewness
	- when ur data is not symmetric, mean and median now give u different answers
	- skewness describes the shape of your distribution
		- balanced on both sides or tail in either direction?

	- ![[Screenshot 2026-02-07 at 3.10.43 PM.png]]
	- ML models perform poorly on skewed data, we need to apply mathematical tricks to make it more symmetric before training

- Kurtosis
	- skewness tells us ab the assymetry of our data
	- kurtosis tells us abut the tails
	- how likely are the outliers compared to a normal distribution

- ![[Screenshot 2026-02-08 at 11.12.28 AM.png]]
- tells u if data has heavy tails (more outliers) or light tails (few outliers) compared to a normal distirbution
- ![[Screenshot 2026-02-08 at 11.14.29 AM.png]]
- ![[Screenshot 2026-02-08 at 11.14.47 AM.png]]
- ![[Screenshot 2026-02-08 at 11.15.04 AM.png]]



- Measures of spread (dispersion)