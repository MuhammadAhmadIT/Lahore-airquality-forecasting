# Predicting Tomorrow's PM2.5 in Lahore

**Author:** Muhammad Ahmad

## About this project
Lahore has very bad air pollution, especially in winter (smog). In this project I used daily air
quality data to predict **tomorrow's PM2.5** from today's data. I also checked if adding weather
data (temperature, humidity, wind and rain) makes the prediction better.

## Questions I wanted to answer
1. Is PM2.5 higher in some months than in others?
2. Can a model predict tomorrow's PM2.5 better than a simple guess?
3. Does weather data help?
4. Where does the model make big mistakes?

## Data
| What | Source |
|---|---|
| Daily air quality for Lahore (2022-08-04 to 2026-02-18) | Kaggle dataset "Air Quality Dataset for Lahore" by Nitiraj Kulkarni (licence CC0 in its metadata) |
| City location | `city_info.csv` from the same dataset (GeoNames) |
| Daily weather (temperature, humidity, wind, rain) | Open-Meteo Historical Weather API, downloaded inside the notebook |

**Important:** the air quality values come from a computer model (CAMS), so they are **not** readings
from real ground monitors. My results show what this model data says about Lahore.

## What I did
1. **Loaded and cleaned the data.** The file has 1,298 rows. The first 3 days were empty, so I removed
   them. After that I had 1,295 days, with no skipped days and no duplicate dates.
2. **Looked at the data with charts.** PM2.5 over time, and the average PM2.5 of each month.
3. **Downloaded the weather** for Lahore and joined it with the air quality data by date.
4. **Prepared the data.** The target is tomorrow's PM2.5. The inputs are today's pollutants, PM2.5 from
   yesterday and last week, the 7 day average, the month, and 4 weather columns (15 inputs in total).
5. **Split by date, not randomly.** Train: 2022-08-11 to 2025-02-28 (933 days). Test: 2025-03-01 to
   2026-02-17 (354 days). The test part includes the last smog season.
6. **Compared models** with 2 simple guesses (tomorrow = today, and the average of that month) and
   3 models (Linear Regression, Random Forest, Gradient Boosting) with fixed settings.
7. **Checked the mistakes** by season, by pollution level and on the biggest mistakes.
8. **Checked which inputs matter most** with the Random Forest.
9. **Tested weather** by training the same model with and without weather.

## Results

### PM2.5 by season
- PM2.5 is highest in January (about 140) and December (about 117), and lowest in April (about 38).
- Smog season (October to January) average: **108.9**. Other months: **50.8**. That is about 2 times higher.
- 99.7% of the days are above the WHO daily guideline of 15.

### Models on the test days
MAE is the average mistake in PM2.5 units (lower is better).

| Model | MAE |
|---|---|
| **Linear Regression (best, R2 = 0.83)** | **12.9** |
| Random Forest (average of 3 runs) | 13.1 |
| Guess: tomorrow = today | 14.1 |
| Guess: month average | 20.9 |

I also trained a Gradient Boosting model, but it did not beat the Linear Regression.

- The best model is only **8.6% better** than the simple guess "tomorrow = today". Today's PM2.5 already
  tells a lot about tomorrow.
- Today's PM2.5 was by far the most important input (about 91% in the Random Forest). The predictions
  follow today's value very closely, so the model mostly copies today.

### Where the model makes mistakes
- The biggest mistake was on 2025-05-22: the real PM2.5 was about 152, but the model predicted about 45.
- The model is weak on sudden jumps and drops. On the 20 days when PM2.5 changed by more than 40 in one
  day, the average mistake was about 43, while the overall average was about 13.

### Does weather help?
Weather did not clearly help.

| Model | MAE without weather | MAE with weather | Change |
|---|---|---|---|
| Random Forest (average of 3 runs) | 13.22 | 13.12 | -0.7% |
| Linear Regression | 12.88 | 12.89 | +0.1% |

The changes are very small, and they are about the same size as the normal difference between runs of the
Random Forest. One possible reason is that today's PM2.5 already contains a lot of the weather
information, because smog and weather change together. I did not test this idea.

## Limitations
- The data is from a computer model (CAMS), not from ground monitors.
- It is one city and about 3.5 years. My test period has only one smog season.
- I did not tune the model settings.
- I picked the best model using the test data, which is a little optimistic.
- The daily average hides hourly peaks.
- I only predict 1 day ahead, and I do not use data about pollution sources (traffic, burning, industry).
- I tested weather on only one test period, so the result could be different with other data.

## What I can do next
- Check my results with real monitor data (for example from OpenAQ).
- Predict 2 or 3 days ahead.
- Try other cities like Karachi and Islamabad.

## How to run
1. Open `Air_quality.ipynb` in Google Colab (File, then Upload notebook).
2. Run all the cells from the top. When Colab asks, upload `air_quality_historical.csv` and
   `city_info.csv` (they are in the `data` folder).
3. The weather download needs internet. If it fails, the notebook continues without weather.

## Files
```
├── README.md
├── Air_quality.ipynb
├── air_quality_historical.csv
└── city_info.csv
```

## Acknowledgements

Dataset by Nitiraj Kulkarni (Kaggle). Weather data from Open-Meteo.
