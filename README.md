# Sweet and Salty Analysis on Nutrition of Online Recipes
Daniela Navarrete  
Final project for DSC 80 at UCSD.
### Introduction
The dataset I will be working with contains information about recipies posted online in food.com from 2008 to 2017. My analysis is centered around exploring the nutritional content of recipes and its relationship with a recipe's average rating and other features in the dataset. Specifically, I will exploring whether higher rated recipes have a different distribution of sodium content compared to lower rated recipies. This analysis is important because nutritional information may be of particular importance to certain groups of people, especially those with dietary restriction.

This dataset contains 83782 rows. Relevant columns for answering my question include:

 `avg_rating`: The average rating for a recipe.  
 `tags`: The food.com tags a recipe had.  
 `nutrition`: The nutritional information of each recipe, including the number of calories and the total fat, sugar, sodium, protein, saturated fat, and carbohydrates as percentages of daily value.  
 `n_steps`: The number of steps in a recipe.  
 `ingredients`: The different ingredients in a recipe.
 `submitted`: The data a recipe was posted.

### Data Cleaning and Exploratory Data Analysis
In order to prepare this dataset for analysis, first, I filled ratings of 0 with `np.nan`. This is because on food.com, ratings are from 1 to 5, so 0 indicates that the rating is missing  or invalid. I expanded the `nutrition` column, which contained lists formatted as strings, into 7 distinct columns (one for each nutritional category included). This was so that I could look at the distribution of sodium and other contents individually. I repeated the same process for `tags` and `steps`. I also changed values in the `submitted` column, which contains when the recipe was posted, to datetime objects, so that I could then extract the year and add it as its own column.  
I also added a new column with rounded values from the `avg_rating` column, which could be helpful for making visualizations.
Lastly I added a column which I calculated by taking the negative sum of sodium, sugar, and saturated fat. I created this column in order create a score where a higher score is generally more favorable, in terms of nutritional content, and lower is less favorable.
<iframe
  src="assets/ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/sodium_full.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/sodium_zoom.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Assessment of Missingness
I think that the missing values in `avg_rating` are MNAR. I believe the probability that a recipe has a missing average rating depends on the value of the average rating. A missing average rating likely means that users did not provide a rating at the time of posting their review, which could be more likely for recipes that would have recived a lower rating. 

Another column with missing data is `description`. In order to decide whether it was MCAR or MAR, I conducted various permutation tests with `description` and other columns and used a significance threshold of p >= 0.05. In each test, I created a column that contained boolean values indicating whether `description` had a missing value or not, shuffled this column, and then computed the absolute difference of means between values of the other column where the description was missing and those where it was not. I found evidence that suggests that the missingness is dependent on `n_ingredients` and is not dependent on `avg_rating`.

<iframe
  src="assets/missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Hypothesis Testing
**Null Hypothesis**: Recipes with four or five stars have the same distribution of sodium content and recipes with one, two, or three stars.
**Alternative Hypothesis**: Recipes with four or five stars do not have the same distribution of sodium content and recipes with one, two, or three stars.

I chose to use absolute difference in means as my test statistic and used a significance level of p >= 0.01. The resulting p-value was 0.015 and therefore, we fail to reject the null hypothesis and conclude that there is likely no significant difference in the distributions. I chose absolute difference in means becuase I wanted to detect differences in either direction. I decided on this significan threshold becuase since the dataset is so large, I wanted to make sure I would detect meaningful difference and not just tiny differences in the distributions. 
<iframe
  src="assets/hypothesis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Framing a Prediction Problem
For building a predicitve model, I want to explore whether we can predict a recipe's sugar content as a percentage of daily value given the recipe's ingredients and average rating. This is a regression problem, since we are predicting a continuous, numerical variable. In order to train the model, I will only be using columns that do not directly contain nutritional information, since if we don't have acess to sugar information, we most likely also will not have access to other nutritional information. I am using R^2 as my scoing metric, since there is a lot of variance in the sugar (PDV) column and I specifically wanted to know how much of that variance was being explained by the features I chose.

### Baseline Model
My baseline model uses the ingredients column and the average ratings column. The ingredients column is categorical, and I encoded it using the bag of words model, with CountVectorizer(). The average ratings column is numerical and I passed it through COlumnTransformer() as is. The score (R^2) I achieved on my testing data is 0.009. I do not think this is a very good model. The score is very low, which indidcated that the features do not explain much of the variance in sugar.

### Final Model
### Fairness Analysis