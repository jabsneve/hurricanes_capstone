# Hurricane Severity Index and Prediction Model

Since 1973 the Saffir-Simpson Hurricane Wind Scale, better known as the Saffir-Simpson Scale, has been the most widely used classification method for tropical cyclones in the Western Hemisphere aka hurricanes. While this scale does serve the purpose of classifying hurricanes based on wind speed it doesn’t address a few important features of tropical cyclones. 

First, the scale is only able to be used for rating hurricanes – storms with maximum sustained winds of at least 64 knots (74 mph). This leaves out any rating of tropical cyclones such as tropical depressions and tropical storms, whom while having lower sustained winds can still generate destructive force on coastal areas.

The other feature not captured by the Saffir-Simpson scale is the size of the storm. This can be measured by the circumference of the storm’s wind field. Simple logic tells us that the larger the area covered by hurricane force winds the larger the area of devastation. 

In 2006 Chris Hebert and Bob Weinzapfel released a new method for measuring tropical cyclone severity named the Hurricane Severity Index (HSI). This index considers both wind speed and wind field size in order to provide a scale more representative of the destructive nature of tropical cyclones. What I have done in this project is utilize data in order to re-create the index then create multiple machine learning models that can be used to predict hurricane category and severity based on storm features.

### Data

NOAA's National Hurricane Center maintains datasets of historical tropical cyclones. The dataset I used for this project is the revised hurricane database, HURDAT2, which includes observations taken from each storm in the Atlantic basin from 1851 to 2019. Wind field radii were not tracked previous to 2004 therefore data from 2003 and earlier were not used for this project. 

* [HURDAT format](https://www.nhc.noaa.gov/data/hurdat/hurdat2-format-nov2019.pdf)
* [HURDAT2 Data (txt)](https://www.nhc.noaa.gov/data/hurdat/hurdat2-1851-2019-052520.txt)
* [Saffir-Simpson Hurricane Wind Scale](https://www.nhc.noaa.gov/aboutsshws.php)
* [Hurricane Severity Index: A More Efficient Way of Predicting a Tropical Cyclone's Destructive Potential](https://ams.confex.com/ams/29Hurricanes/techprogram/paper_168529.htm)

### Data Cleaning

After converting the text data from the HURDAT2 dataset to a csv file I uploaded the data into my Hurricanes notebook for cleaning and processing. The information was fairly orderly with a few issues needing to be addressed – which included:

* Inconsistencies in storm names
* Inconsistencies in dates
* Erroneous time measurements
  * The NHC's official measurements were recorded in 6-hour intervals (0000, 0600, 1200, 1800)
  * Many observations taken at inconsistent times outside of the proper intervals produced erroneous data which had to be removed
* Cyclones with maximum sustained winds of less than 30 knots are not included in the HSI and were removed from the dataset

### EDA & Feature Engineering

The Hurricane Severity Index is the summation of two 25 point scales, one for wind speed intensity and the other for size. To calculate wind speed intensity points was a straight-forward standardization formula where 30 knots equaled 0 and 150 knots equaled 25.

Calculating the points awarded to storms for size was more tricky. Due to the inconsistent nature of which the winds reach out from the center of the storm the wind fields had to be standardized to create symmetrical circles. This was accomplished by using the formula below to convert the quadrant radii to a complete diameter.

![Wind Field Formula](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/wind_field_formula.png)

Next I standardized the wind field size to each wind threshold's respective point scale as shown below.

Wind Threshold | Point Range
---------- | -----------
34 kts | 1-3
50 kts | 1-4
64 kts | 1-8
87 kts | 1-10

Once this was complete the intensity points and size points were added as a total Hurricane Severity score for each storm.

Here we see the number of storms each year and the average severity of those storms.

![Named storms per year](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/storm_count.png)
![Average storm severity per year](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/avg_severity.png)

### Machine Learning Models

For my predictive models I chose a k-Nearest Neighbors model for predicting storm categories and a linear regression model for severity prediction. Both models made their predictions using minimum (barometric) pressure with the idea that lower barometric pressure will correlate to a stronger, more severe storm.

For the k-Nearest Neighbors model I first tested a range of neighbors, 1 through 9, and found that the most accurate number of neighbors to predict on would be 5

![Selecting the number of neighbors](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/knn_accuracy.png)

Below you'll see the results of the model's prediction of each storm category.

![Classification report](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/classification_report.png)

With the goal of predicting storm severity, which is a continuous variable, from a single feature, minimum pressure, it seemed obvious to implement a simple linear regression model here. After running the model it was found it had an R-squared value of 94.2% and a Root Mean Squared Error of 2.73.

![Predicted vs actual severity](https://github.com/jabsneve/hurricanes_capstone/blob/main/Images/severity_regression.png)

### Conclusion

While this is not a very complex data set it does provide us with enough information to see that there is a strong correlation between minimum pressure and storm severity. This knowledge provided the ability to make two simple yet accurate models for storm predictions of both categorical variables and continuous variables.

### Future Improvement

I would like to add more feature data such as sea surface temperature, humidity, etc. to create much more robust models capable of greater accuracy and precision.
