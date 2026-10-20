# How much money should Tarik Skubal receive this offseason? A Study From the Perspective of the Chicago Cubs

**IST 707 Midterm Report**
Kalen Muehlig, Owen St.Onge, Justin Muttreja
Professor McSweeney
16 October 2026

---

## Introduction (Problem and Stakeholders)

A majority of the resources that Major League Baseball teams put towards analytics contribute to player projections and eventually contract evaluation. Teams are constantly working to get players at a premium and to discover hidden talent before the rest of the league, especially for younger or amateur players. For elite and established players, contract evaluation is about not overpaying rather than finding hidden talent.

Tarik Skubal is a pitcher for the Los Angeles Dodgers who entered the 2026 season coming off back-to-back American League Cy Young Award Winning seasons (awarded to the best pitcher in the league) and is a consensus top ten player in the sport. After the conclusion of the 2026 World Series, he will be a free agent and can sign to whichever team gives him the highest offer.

Posing as the front office of the Chicago Cubs for this project, we need to determine Skubal's objective monetary worth. The Chicago Cubs are a team that will spend big money and had the 7th highest payroll in the MLB in 2026, but are financially responsible compared to the teams at the top and are unlikely to give a large overpay to a single player in free agency. To do this, we will create machine learning models to place value on Skubal and determine what the maximum contract we would offer him is.

Our main stakeholders are the upper management of the Cubs's front office (GM, President of Baseball Ops) as well as the ownership group. A secondary stakeholder will be Skubal's agent, Scott Boras, as he also needs to be convinced that what we value him at is accurate and not underselling him. Due to both of these stakeholders, it is not only important that we can predict what Skubal should get paid, but we should also be able to explain why. This is a very important part of the negotiation process as being able to explain what influences these projections is what will help the GM and ownership group make that informed decision as well as prevent Skubal's agent from seeking a higher monetary value for no justifiable reason.

## Related Work

Predicting MLB contracts is something that has been done numerous ways by different people. There are many common ways of predicting a player's contract with the most popular being based on a player's future projected WAR output. WAR (Wins Above Replacement) is a common measure used to quantify how many wins a player is bringing to a team relative to a replacement level player. This measure looks at a variety of aspects of the game such as a player's production on the plate as well as their effectiveness in the field. Fangraphs currently has the market set at 8 million dollars per 1 WAR, so if a player finished the season with 5 WAR, they would be worth 40 million dollars for that season.[^1] This idea is taken and applied to a player's future projected WAR, so if a player is projected to have 25 WAR over the next 7 years, then a player would be projected to receive a 7 year/200 million dollar contract with this methodology. This was common practice for many years and as of recently this idea has evolved as dollar/WAR is no longer standard across all players. As has been observed with the previous free agent class, superstar players went for a higher dollar/WAR amount compared to replacement or above average player as from 2020-2023, free agents who were projected over 2 WAR received over 40% more dollar/WAR than players who were projected between 0 and 1 WAR.[^2] This non-linearity in dollar/WAR value has been something that many older models have not implemented which can make a great addition to the new models we intend to build.

Going along the lines of projecting based on WAR, aging curves are another common component to contract projections. These look at the fluctuations in player performance as they age.[^3] Many aging curves typically have a concave down like shape, where a player sees an increase in performance from their early 20s to their peak which is their late 20s-early 30s, and then a decline in performance as they age into their 30s. These are commonly used in WAR projections to see how a player's WAR will increase or decrease as they age. Similar to how dollar/WAR changes across time, aging curves can also age across time as players from the 1990s have different aging curves than the players who play today.[^4] This is similar to why constantly updating the aging curve to see what new patterns or trends emerge can be very important as older curves tend to be less reliable estimates.[^5]

## Dataset

Statcast is our main source of data for this project. Statcast is an MLB-run organization that records, stores, and compiles statistics from baseball games to a very detailed degree. Each baseball game returns several terabytes of data. While only some of this is made available to the public, Statcast does a great job of sharing a lot of their data and information. One of the sources they share their data with is FanGraphs, a popular baseball and sabermetrics website that has become one of the number one sources of anything related to baseball and baseball data. FanGraphs allows you to scrape their site for season-aggregate data, which is what we will be using for this project. This data was scraped for free using the PyBaseball package in Python. Because Statcast has only existed since mid-2015, only data since 2016 was scraped. Given that Tarik Skubal is a pitcher, we exclusively scraped pitcher data.

The data had 9,836 unique pitcher-seasons. The data is sparse, and has 548 features. Most of these features pertain to small nuances in the game, such as the exact movement in inches of each pitcher's pitches or the angle of the bat the opposing hitter attacks the pitch with on average.

The variables we selected to use in the model (listed below) were based on our personal expertise in the subject. We wanted to include stats that would typically be presented in a season-aggregate setting which describe results and peripherals rather than smaller-scale details that might not describe success on their own. Included below are some indicators for the pitchers as well as the variables included in the first batch of models:

| Variable Type | Variable | Description |
|---|---|---|
| Identifier | PlayerName | Pitcher's name |
| | Position | What position they play (in this case it's pitcher for every row) |
| | Throws | What hand they pitch with |
| | Season | What season/year it is |
| Context | Age | How old the pitcher was/is |
| Result-based Metrics | ERA | Pitcher's ERA |
| | FIP | Pitcher's FIP |
| | WHIP | Pitcher's WHIP |
| Rate Metrics | K% | Pitcher's strikeout percentage |
| | BB% | Pitcher's walk percentage |
| Valuation Metrics | WAR | Pitcher's WAR |
| | Dollars | How many dollars a pitcher was worth in a given season |
| Velocity and Contact Allowed | FBv | Fastball velocity |
| | Stuff+ | A grade describing the quality of a pitcher's pitches |
| | Barrel% | Pitcher's Barrel% |

## Methods

When creating the train and test sets, we were concerned about the model having an easier time overfitting if pitchers were in both the training sets and the test sets (i.e. Jacob DeGrom 2021 in the train set, Jacob DeGrom 2022 in the test set) because the model might be able to memorize pitcher profiles which would lead to a biased test set. To avoid this, the train test split was performed so that no pitcher would exist in both the training set and the test set.

Eventually, the goal will be to predict multiple years into the future using multiple models, i.e. one model predicting one year into the future, one model predicting two years into the future, one model predicting three years into the future, etc. This is necessary because the models we deal with in class are single-output models. For now, in this mid-term report we have only spent time on predicting one-year into the future and learning all we can from that modeling process.

We will run 3 models: a decision tree, gradient boosting regressor, and an elastic net. The reason an elastic net was used specifically is because we were worried about overfitting and because many of the feature inputs have multicollinearity, so the coefficient estimates will be biased if they are not properly regularized. The models use current-season data to predict the WAR value of a player's next season. We will use RMSE as an evaluation metric because we are not concerned with outliers in the data given the distribution. We will compare the models. This report is not a final solution, but rather something we can learn from as we continue to refine our models going forward.

## Exploratory Data Analysis

### EDA 1 - Relationship between WAR and Dollars

![WAR vs. Dollars](figures/01_war_vs_dollars.png)

The first plot is to look at the relationship between WAR and dollars. WAR is perfectly linear with Dollars as Dollars is calculated directly from WAR. One will be the target variable while the other will have to be completely left out of the model as having one as a target and one as a feature could leak the answer to the model.

### EDA 2 - Distribution of WAR

![Distribution of WAR](figures/02_war_distribution.png)

![Distribution of Dollars](figures/03_dollars_distribution.png)

The next two plots look at the distributions of both WAR and Dollars. Given that both have a perfect linear relationship, they have the exact same distribution shape. Notice the median for WAR is 2.5 and Dollars is $20 million (2.5 × 8 = 20) reflecting the conversion between the two variables. Analyzing both distributions, they are right-skewed as there tends to be a few pitchers (such as Skubal) with a much higher value compared to the rest of the population.

### EDA 3 - Relationship between FIP and WAR

![FIP vs. WAR](figures/04_fip_vs_war.png)

This plot looks at the relationship between FIP and WAR. FIP looks at the quality of a pitcher without factoring the team's defense. Lower FIP indicates a better performance. FIP is part of the calculation for WAR, but unlike converting WAR to Dollars, there are other variables (such as innings pitched and ballpark factors) which is why there is not a perfect relationship. Skubal's FIP this year was 2.51 making him harder to predict as there are fewer pitchers with a FIP under 3.

### EDA 4 - Relationship between Age and WAR

![WAR by Age (LOWESS Curve)](figures/05_war_by_age.png)

The fourth plot looks at a LOWESS curve for WAR and Age. Typically, aging curves follow an upside-down U-shaped pattern which is not the case here. Here there is more of a gradual decline as pitchers age. Our primary suspicion is survivorship bias as only a few older pitchers were effective enough to meet our filter of at least 130 innings pitched and start 25 games. Skubal will be 30 next season, so this is important to keep in mind when deciding the number of years.

### EDA 5 - Correlation and Heatmap

![Correlation Heatmap of Key Pitcher Variables](figures/06_correlation_heatmap.png)

Our final plot is a correlation heatmap which allows us to see which variables have signal and which have some overlap. WAR has a strong negative correlation with ERA, FIP, and WHIP as the lower these values are, the better the pitcher tends to be. ERA, FIP, and WHIP all have a strong positive correlation with each other. This is something to keep in mind while modeling, especially when thinking about multicollinearity concerns.

## Baseline Model

We needed to create a baseline model so we can compare our models to it. Although RMSE is interpretable because it is in the same unit as the response variable, we have no idea if our model is performing well unless we have a reference point. To create this reference point, we took the mean of the response variable for all instances in the training set and used it for the baseline prediction for every instance of the test set. This way, the baseline just predicts the most common value for everybody, and we can see how our actual predictions deviate from what a model that knows no information would guess.

![Baseline (Mean) Predictions vs Actual: Test Set](figures/07_baseline_predictions.png)

It can be seen above how the baseline model predicts upon the test set. Most of the data is centered from 0-2 WAR because this is the average value, so the model is not too far off here, but it does a bad job when it comes to better performing players. The test RMSE for the baseline is 1.593.

## Best Model So Far and Future Improvements

As previously mentioned, three models were created: a decision tree, a gradient boosting regression model and an elastic net. We fit each model, ran predictions on both the train and test set, and calculated train and test RMSE. The results can be seen in the chart below, with the blue bars representing train RMSE and the orange bars representing test RMSE.

![Train vs Test RMSE by Model](figures/08_rmse_by_model.png)

The decision tree badly overfit. The train RMSE was 0 and the test RMSE was much higher than that of the baseline model, indicating that the model memorized all the training data rather than creating any generalizations or discovering any trends. Nothing about the model was tuned which could have contributed to the issue, but we did not expect this model to outperform the other models anyway, and this result was expected and accepted. The gradient boosting model and the elastic net performed very similarly, with the gradient boosting model outperforming the elastic net by .006. There is little to no difference between these two models in terms of performance, but the best model is the gradient boosting regressor. In comparison to the baseline, these models perform about 0.3 WAR better on average, which is a meaningful difference as 0.3 WAR can equate to millions of dollars, but isn't as good as we expected, which we will talk about later. The gradient boosting regressor was tuned with a small tuning grid, something that could definitely be expanded in a future iteration of the model, and the elastic net was tuned on a larger grid with less parameters to tune. Therefore, although these models performed similarly, we think that there is more room for improvement with the gradient boosting model.

![Gradient Boosting Predictions vs Actual: Test Set (RMSE = 1.299)](figures/09_gb_predictions.png)

Above can be seen the test set prediction results against the observed values for the gradient boosting regressor. There is more shape than the baseline model and a clear positive trend. A major issue is that the model consistently underpredicts WAR for players who post 3.0 WAR and over. This may be because of distribution imbalance but also may be because of a failure of our model to account for playing time. Because WAR is a counting stat and not a rate stat, players who play more will see higher WAR values. This is not reflected in the model, and needs to be in a future iteration.

**Gradient Boosting Tuning Results** (best combination in bold)

| Rank | n_estimators | learning_rate | max_depth | CV RMSE Mean | CV RMSE Std |
|---:|---:|---:|---:|---:|---:|
| **1** | **100** | **0.05** | **2** | **1.296** | **0.066** |
| 2 | 100 | 0.05 | 3 | 1.300 | 0.068 |
| 3 | 100 | 0.1 | 2 | 1.309 | 0.069 |
| 4 | 300 | 0.05 | 2 | 1.314 | 0.072 |
| 5 | 100 | 0.1 | 3 | 1.317 | 0.071 |
| 6 | 300 | 0.05 | 3 | 1.318 | 0.072 |
| 7 | 300 | 0.1 | 2 | 1.330 | 0.078 |
| 8 | 300 | 0.1 | 3 | 1.343 | 0.080 |

In the future, the model could also be tuned more. Only eight combinations were explored and the values chosen for the grid are arbitrary. Three of the four best models had the smallest possible learning rate, indicating that the learning rate may need to be smaller. The model can be trained with a larger grid or using a tuning algorithm in the future to find more optimal hyperparameters, which may separate it further from the elastic net in terms of performance.

## Problems/Challenges

Many of the problems and challenges have already been described in this paper. In MLB front offices, projection systems are usually Bayesian Hierarchical Models which can capture random effects and smooth curves to better handle age and projection multiple seasons into the future. They can also be neural networks which can return multiple outputs and therefore return projections for multiple seasons into the future. The models used in this class cannot do these things without venturing beyond the scope of the course, so to project multiple seasons into the future we will have to eventually make multiple models.

We also failed to consider playing time indicators in our model. It can be dicey to include a variable detailing the number of games played in the previous season because of injuries, so we may have to do some more advanced feature engineering, maybe even creating a supplementary model to project playing time. If this does not fix the issue of the model failing to capture higher-WAR seasons, we may need to undersample players with average WAR.

---

[^1]: Clemens, "What are Teams Paying For A Win In Free Agency? 2026 Edition"
[^2]: Clemens, "What are Teams Paying For A Win In Free Agency? 2026 Edition"
[^3]: Slowinski, "Aging Curve"
[^4]: Clemens, "What are Teams Paying For A Win In Free Agency? 2026 Edition"
[^5]: Slowinski, "Aging Curve"
