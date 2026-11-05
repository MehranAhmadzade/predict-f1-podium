# 🏁 Who Will Be on the Podium This Weekend?

- If you're a Formula 1 fan, you already know what a podium is: the top 3 drivers at the end of the race. 🏆
- This project tries to predict who will finish there.
- The problem may look easy, but solving it well turned out to be quite interesting.

## Algorithms

The project uses four machine learning algorithms:

- Logistic Regression
- SVM
- XGBoost
- Random Forest

Each model is evaluated with several metrics, including one I made myself, which I explain below.

## Data

The data comes from Ergast F1 dataset and is stored locally as CSV files in the `dataset` folder. It covers 1950 to the middle of the 2026 season, but I only use races from **2014 onward**, because that's when the hybrid-engine era started also Older races were run under very different conditions and would add noise.

**Feature engineering**

- I used columns such as grid position, sprint results, and driver and constructor points and wins.
- Qualifying times (q1, q2, q3) are converted to a ratio: `fastest_time / your_time`. The fastest driver gets 1, everyone else gets less than 1, and a driver with no time gets 0.
- I created features such as `podiums_won_before`, `driver_wins` and `driver_points`. They only use information from **previous** races, never the race being predicted, to avoid data leakage.
  I care a lot about data leakage. Unlike Ferrari, who seem to care less about leaks in their car. 🔧💦

**Train/test split:** I split by season instead of randomly because a random split would put drivers from the same race in both train and test sets, and my metric needs whole races.

## Average Podium Prediction

Evaluating the models was harder than I expected. I tried several metrics, including PR-AUC and F1-score (which has nothing to do with Formula 1, believe it or not 😄), but none of them told me what I really wanted to know: **for each race, how many of the real podium drivers did the model get right?**

So I built my own F1-specific metric. For each test race, the model picks the 3 drivers with the highest scores, and I count how many of them actually made the podium. The final result is the average over all races. It also shows how many races got 0, 1, 2 or all 3 podium drivers right.

For example, if Hamilton, Verstappen (DU DU DU 🎶) and Leclerc (I doubt it 🤔) take the podium, and the model predicts Hamilton and Verstappen but not Leclerc, that race scores 66.7% meaning 2 of 3 podiums were caught by the model. then we will get an average of this metric for all races.

## Results

| Model               | Avg. accuracy per race | 0 correct | 1 correct | 2 correct | 3 correct |
| ------------------- | ---------------------- | --------- | --------- | --------- | --------- |
| XGBoost             | 70.26%                 | 0%        | 13.85%    | 61.54%    | 24.62%    |
| Logistic Regression | 69.23%                 | 3.08%     | 16.92%    | 49.23%    | 30.77%    |
| Random Forest       | 68.72%                 | 1.54%     | 15.38%    | 58.46%    | 24.62%    |
| SVM                 | 64.62%                 | 1.54%     | 24.62%    | 52.31%    | 21.54%    |

Fortunately or unfortunately, all four models performed very close to each other on nearly every metric. Meaning on average about 2 of the 3 podium drivers per race were predicted correctly. XGBoost stood out slightly and it got at least one podium driver right in every race, with no race scoring 0.

## What I'd Add Next

Even though I'm happy with this project, it's far from perfect:

- Use the FastF1 API to fetch the data for an upcoming race automatically, instead of typing it in by hand, which is painful.
- Try new models and more data to improve the results.
- Create more features from the existing columns.

## How to Run

1. Clone the repo and install the requirements: `pip install pandas numpy scikit-learn xgboost matplotlib`
2. Make sure the CSV files are in the `dataset` folder.
3. Open `podium.ipynb` and run all cells.

Hope you enjoyed it and learned something about F1 and machine learning! 🏎️
