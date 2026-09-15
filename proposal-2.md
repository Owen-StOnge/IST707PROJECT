Kalen Muehlig, Owen St.Onge, Justin Muttreja
Professor McSweeney
IST 707
14 September 2026
Project Proposal

# How much money should Tarik Skubal receive this offseason? A Study From the Perspective of the Chicago Cubs

## Team
**Bold indicates the owner of the repository for this project**
- **Kalen Muehlig** - Kmuehlig
- Owen St.Onge - Owen-StOnge
- Justin Muttreja - jmutt05

## Introduction
A majority of the resources that Major League Baseball teams put towards analytics contribute to player projections and eventually contract evaluation. Teams are constantly working to get players at a premium and to discover hidden talent before the rest of the league, especially for younger or amateur players. For elite and established players, contract evaluation is about not overpaying rather than finding hidden talent.

Tarik Skubal is a pitcher for the Los Angeles Dodgers who entered the 2026 season coming off back-to-back American League Cy Young Award Winning seasons (awarded to the best pitcher in the league) and is a consensus top ten player in the sport. After the conclusion of the 2026 World Series, he will be a free agent and can sign to whichever team gives him the highest offer.

Posing as the front office of the Chicago Cubs for this project, we need to determine Skubal's objective monetary worth. The Chicago Cubs are a team that will spend big money and had the 7th highest payroll in the MLB in 2026, but are financially responsible compared to the teams at the top and are unlikely to give a large overpay to a single player in free agency. To do this, we will create machine learning models to place value on Skubal and determine what the maximum contract we would offer him is.

## Literature Review
Predicting MLB contracts is something that has been done numerous ways by different people. There are many common ways of predicting a player's contract with the most popular being based on a player's future projected WAR output. WAR (Wins Above Replacement) is a common measure used to quantify how many wins a player is bringing to a team relative to a replacement level player. All-stars tend to have 5-6 WAR by the end of the season while MVP candidates are usually above 8 with all time seasons being around 10 WAR. This measure looks at a variety of aspects of the game such as a player's production on the plate as well as their effectiveness in the field. Fangraphs currently has the market set at 8 million dollars per 1 WAR, so if a player finished the season with 5 WAR, they would be worth 40 million dollars for that season (Clemens). This idea is taken and applied to a player's future projected WAR, so if a player is projected to have 25 WAR over the next 7 years, then a player would be projected to receive a 7 year/200 million dollar contract with this methodology. This was common practice for many years and as of recently this idea has evolved with the dollar/WAR is no longer standard across all players. As has been observed with the previous free agent class, superstar players went for a higher dollar/WAR amount compared to replacement or above average player as from 2020-2023, free agents who were projected over 2 WAR received over 40% more dollar/WAR than players who were projected between 0 and 1 WAR (Clemens). This non-linearity in dollar/WAR value has been something that many older models have not implemented which can make a great addition to the new models we intend to build.

Going along the lines of projecting based on WAR, aging curves are another common component to contract projections. These look at the fluctuations in player performance as they age (Slowinski). Many aging curves typically have a concave down like shape, where a player sees an increase in performance from their early 20s to their peak which is their late 20s-early 30s, and then a decline in performance as they age into their 30s. These are commonly used in WAR projections to see how a player's WAR will increase or decrease as they age. Similar to how dollar per WAR changes across time, aging curves can also age across time as players from the 1990s have different aging curves than the players who play today (Clemens). This is similar to why constantly updating the aging curve to see what new patterns or trends emerge can be very important as older curves tend to be less reliable estimates (Slowinski).

Our main stakeholders for this project would be the upper management of the front office (GM, President of Baseball Ops) as well as the ownership group. A secondary stakeholder will be Skubal's agent, Scott Boras, as he also needs to be convinced that what we value him at is accurate and not underselling him. Due to both of these stakeholders, it is not only important that we can predict what Skubal should get paid, but we should also be able to explain why. This is a very important part of the negotiation process as being able to explain what influences these projections is what will help the GM and ownership group make that informed decision as well as prevent Skubal's agent from seeking a higher monetary value for no justifiable reason.

## Data and Methods

### Data
Data Links:
- https://baseballsavant.mlb.com/
- https://www.spotrac.com/
- https://github.com/jldbc/pybaseball

The above sources are the three main sources that we will be using for this project. Spotrac is the main source of contract data which gives information on the total money, length of the contract, average annual value, and everything relating to the overall structure of the contract. Baseball savant and pybaseball both contain data on numerous variables that will be used for modeling such as a player's age, spin rate, velocity, arm angle, whiff rate, etc. These datasets can be hundreds of columns, but for our purposes we would filter the data down to our columns of interest.

Given that Tarik Skubal is a pitcher, we would focus on metrics used to evaluate pitchers such as spin rate, velocity, etc. as opposed to hitter metrics like wRC+ or stats of that nature. In addition to that, we will be focusing on pitchers as predicting the market for a pitcher is entirely different from the market for a hitter. These sources are very reliable as they are frequently used by every MLB team as well as numerous research projects. They serve as the standard websites that store this data.

### Methods
Prior to building our models, we will use Principal Component Analysis (PCA) on our selected features to help reduce the dimensionality of our data which will also help reduce any multicollinearity we could encounter in our model. This is especially important for our models as our goal is to not only project what Skubal will make, but also identify what is driving his projection.

After that, we will run 3 models (linear regression, random forest, and XGBoost). We will use multiple evaluation metrics such as RMSE and MAE curves to determine which model performed the best. After we select this model, we will use it to predict Skubal's contract.

## Project Plan

| Period | Activity | Milestone |
|---|---|---|
| 9/18 - 9/21 | Define all variables for modeling | Collect and clean data |
| 10/1 - 10/8 | Perform EDA and observe data trends | Finalize all variables for modeling |
| 10/16 - 10/25 | Implement PCA to reduce dimensionality and aging curve implementation | Assign the final model each person will do |
| 11/1 - 11/5 | Work on final models (train/test split, evaluation metrics, etc.) | Move into model comparison |
| 11/10 - 11/15 | Model comparison and selection; additional time if something does not go according to plan | Select best model and apply it to Tarik Skubal |
| 11/20 - 12/01 | Build out presentation | Submit final project and prepare for presentation on 12/8 |

## Risks
While we don't have to worry about the risks posed in many projects such as data availability or integrity, there are still many pitfalls that our project could face. While we have a lot of data available to us, pitchers of Tarik Skubal's caliber are rare as he is one of the top arms in the MLB. As a result of this, the models may have a difficult time predicting his value due to him being a relative outlier compared to the average MLB pitcher. While the MLB has a few ace caliber pitchers at any given time, there are always changes in the MLB landscape that could affect how much a pitcher receives at a given point in time. In a similar aspect, the MLB is proposing a salary cap, so even if our projection is highly accurate, the change in the financial structure of the league could drastically change the contract that he receives. In terms of mitigation for these risks, some issues can be handled much better than others.

Some problems such as the salary cap implications will be tough to handle because it is unknown if there will even be a salary cap implemented, and if so what will it be, and what other financial rules will accompany it. Unfortunately, due to the uncertainty of it, we will have to assume that the MLB financial structure will remain as currently is with no significant changes. On the other hand, issues such as the rarity of a player of Skubal's caliber will require us to constantly work with the data to see how far back we should go, what pitchers we should include, and what variables are the best predictors of future pitcher performance. Constantly adjusting these factors will help us to produce the most accurate models.

If our models struggle to produce a reliable point estimate, we will have to shift to providing a relative range of where his contract would likely fall. This would avoid any overclaim, and may in fact end up being a better alternative as predicting a relative range for a player's contract is more stable than one number.
