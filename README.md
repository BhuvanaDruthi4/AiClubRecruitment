# AiClubRecruitment
How missing data and anomalies were handled.
MISSING DATA:
To fix this, I used a simple method called forward fill — this means if a value is missing at some hour, I just copy the value from the previous hour
If there were any gaps at the very beginning of the dataset where forward fill couldn't work (nothing before it to copy from), I used backward fill which looks ahead instead.This approach made sense for electricity data because demand doesn't suddenly jump or disappear it changes gradually, so the nearest known value is a reasonable estimate for a short gap.

HALF-HOUR TIMESTAMPS:
The raw data had some half-hour readings along with regular readings,I combined these by first flooring all timestamps down to their hour (so 17:30 becomes 17:00) and then averaging the values that fell into the same hour. This way no information is lost ,both the 17:00 and 17:30 readings contribute equally to the final 17:00 value.

ANOMALIES:
I used IQR(Interquartile Range) to detect the outliers.In this method ,we look at the middle 50% of all demand values and define a reasonable upper and lower boundary around that range,any value outside this range is an outlier and we replace it with median.
Mean is affected by the outliers,if the one of the value is too large the mean value also increases, hence we use median here.


Which temporal and external features were engineered
and why.
CALENDAR FEATURES:
The machine learning model I used (Random Forest) has no built-in understanding of time. It sees each row as just a set of numbers with no connection to the rows before or after it. So I had to manually create features that tell the model what point in time each row represents.
So it has hours of day, day of week,month

LAG FEATURES:
Since Random Forest treats every row independently, it has no memory of what happened in previous hours unless we give it that information. Lag features do this,they add columns that contain past demand values.

lag_1:demand from one hour ago
lag_24:demand from the same hour yesterday
lag_168:demand from the same hour exactly one week ago

WEATHER FEATURES:
Temperature and humidity directly affect how much electricity people use. I merged the hourly weather data with the demand data by matching timestamps so each demand row got the weather conditions from that same hour.

ECONOMIC FEATURES:
I included annual macroeconomic indicators like GDP and population.Since this data is annual, I joined it to the hourly data by year, meaning every hour in 2024 gets the same 2024 GDP value.

Key insights from feature importance.
Generation_mw is the most important feature with an importance score of 0.945 out of 1.The amout of electricity being generated at any hour is almost equal to demand at that hour.

lag_1 is the second most important with a score of 0.022.Most recent hour demand is very likely to be close to the present one.This is the strongest time based feature.

hour is third with a score of 0.011,this shows the model learned the dialy patterns.Different hours have different demands and the model picked up on this.

