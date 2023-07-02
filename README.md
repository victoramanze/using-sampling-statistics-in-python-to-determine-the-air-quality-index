# using-sampling-statistics-in-python-to-determine-the-air-quality-index
Now as as analyst for the Environmental protection agency you been tasked to use effective sampling to analyze the data on air quality .

As said in the last project that this will be a series of projects using statistics with python to analyze this dataset and this is the 3rd project on this series .\

Go to my previous post to view the last 2 post where analysis was done with statistics and this is just one where we use sampling to derive insight

first we import all relevant libraries needed

Import pandas,

numpy,

matplotlib,

statsmodels,

and scipy.

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import statsmodels.api as sm
from scipy import stats
now we import the dataset needed for analysis

pd.read_csv("c4 epa air dataset.csv")

we rename the dataset

ads = pd.read_csv("c4 epa air dataset.csv")
we use the head function to view the first 10 set of data in our dataset

ads.head(10)

based on the first few rows of data seen above that As the units_of_measure column contains parts per million, you can infer that the aqi values are measured in parts per million.

we use the describe(include=’all’) to get the description of the data in the DataFrames

ads.describe(include='all')

Based on the table of descriptive statistics generated above, what do you notice about the count value for the aqi column?**

The count value for the aqi column is 260. This means there are 260 AQI measurements represented in this dataset.

Now, use the mean() function on the aqi column and assign the value to a variable population_mean. The value should be the same mean value the describe() function produced for the ‘aqi’ column.

population_mean = ads['aqi'].mean()
Sample with replacement

First, name a new variable sampled_data. Then, set the arguments for the sample function N, sample size, equal to 50. Set replace equal to “True” to specify sampling with replacement. For random_state, choose an arbitrary number for random seed. Make that arbitrary number 42.

sampled_data = ads.sample(n=50, replace=True, random_state=42)
sampled_data.head(10)

sample_mean = sampled_data['aqi'].mean()
Apply the central limit theorem Imagine repeating the the earlier sample with replacement 10,000 times and obtaining 10,000 point estimates of the mean. In other words, imagine taking 10,000 random samples of 50 AQI values and computing the mean for each sample. According to the central limit theorem, the mean of a sampling distribution should be roughly equal to the population mean. Follow the steps below to compute the mean of the sampling distribution with 10,000 samples.

Create an empty list and assign it to a variable called estimate_list. Iterate through a for loop 10,000 times. To do this, make sure to utilize the range() function to generate a sequence of numbers from 0 to 99,999. In each iteration of the loop, use the sample() function to take a random sample (with replacement) of 50 AQI values from the population. Do not set random_state to a value. Use the list append() function to add the value of the sample mean to each item in the list.

estimate_list = []
for i in range(10000):
    estimate_list.append(ads['aqi'].sample(n=50,replace=True).mean())
Next, create a new DataFrame from the list of 10,000 estimates. Name the new variable estimate_df.

estimate_df = pd.DataFrame(data={'estimate': estimate_list})
estimate_df

Next, compute the mean() of the sampling distribution of 10,000 random samples and store the result in a new variable mean_sample_means

mean_sample_means = estimate_df['estimate'].mean()

mean_sample_means

How are the central limit theorem and random sampling (with replacement) related?

Random sampling with replacement is related to the central limit theorem because it means you are drawing observations independently from a population. The central limit theorem states that if a sample size is large enough and the observations are drawn independently — or with replacement — the sampling distribution of the sample mean is approximately the normal distribution. Furthermore, the mean parameter is the population mean and the variance parameter is the standard error.

Output the distribution of these estimates using a histogram. This provides an idea of the sampling distribution.

estimate_df['estimate'].hist()

Calculate the standard error
Calculate the standard error of the statistic. The standard error of a statistic is the standard deviation of the sampling distribution associated with the statistic. It provides a numerical measure of sampling variability and answers the question: How far is a statistic based on one particular sample from the typical value of the statistic?

standard_error = estimate_df['estimate'].std()
standard_error

Visualize the relationship between the sampling and normal distributions
Visualize the relationship between your sampling distribution of 10,000 estimates and the normal distribution. The code below overlays the density curve of the normal distribution described in the theorem on top of the histogram of the sampling distribution obtained by repeated sampling. The solid green line in the graph is the population mean and the red dashed line is the estimate.

plt.hist(estimate_df['estimate'], bins=25, density=True, alpha=0.4, label = "histogram of sample means of 10000 random samples")
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100) # generate a grid of 100 values from xmin to xmax.
p = stats.norm.pdf(x, population_mean, standard_error)
plt.plot(x, p, 'k', linewidth=2, label = 'normal curve from central limit theorem')
plt.axvline(x=population_mean, color='g', linestyle = 'solid', label = 'population mean')
plt.axvline(x=sample_mean, color='r', linestyle = '--', label = 'sample mean of the first random sample')
plt.axvline(x=mean_sample_means, color='b', linestyle = ':', label = 'mean of sample means of 10000 random samples')
plt.title("Sampling distribution of sample mean")
plt.xlabel('sample mean')
plt.ylabel('density')
plt.legend(bbox_to_anchor=(1.04,1))

What insights did you gain from the sampling distribution above?
The histogram of the sampling distribution is well-approximated by the normal distribution described by the central limit theorem. The estimate based on one particular sample (red dashed line) is off-center. This is expected due to sampling variability. The red dashed line would be in a different location if epa_data.sample(n=50, replace=True, random_state=42) had a different value for random_state. The population mean (green solid line) and the mean of the sample means (blue dotted line) overlap, meaning that they are essentially equal to each other.

Considerations
What are some key takeaways that you learned from this lab?
Sampling with replacement on a dataset leads to duplicate rows. Sample means are different from population means due to sampling variability. The central limit theorem helps describe the sampling distribution of the sample mean for many different types of datasets.

What findings would you share with others?
The mean AQI in a sample of 50 observations was below 100 in a statistically significant sense (at least 2–3 standard errors away). For reference, AQI values at or below 100 are generally thought of as satisfactory. This notebook didn’t examine values outside the “satisfactory” range so analysis should be done to investigate unhealthy AQI values.

What would you convey to external stakeholders?
Carbon monoxide levels are satisfactory in general. Funding should be allocated to further investigate regions with unhealthy levels of carbon monoxide and improve the conditions in those regions………
