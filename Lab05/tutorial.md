# Lab 05: Smart Garden Sensor Analysis

## Submission requirements (Read this first please)

It is a group assignment. One submission per group. You are required to submit the following content to your forked Github repo:

1.  Fork Github repository [ISDN3000C_Lab05_template](https://github.com/siyanhu/ISDN3000C_Lab05_template). 
2.  Create virtual python environment and activate it.
3.  Complete the python scripts: `getting_started.py`, `part1.py`, `part2.py`, and `part3.py`.
4.  Run the following code to create your jupyter notebook for final challenge
    ```bash
    pip install notebook
    ```
    and
    ```bash
    touch final_challenge.ipynb
    ```
    and complete your code in `final_challenge.ipynb`.
5.  Answer the questions in `questions.md` **based on your dataset and codes**.
6.  add `readme.md`, `.gitignore`, and/or `requirements.txt` if necessary.
7.  push following files to remote:
    ```
    ISDN3000C_Lab05 /
    ├── getting_started.py
    ├── part1.py
    ├── part2.py
    ├── part3.py
    ├── final_challenge.ipynb
    ├── readme.md
    ├── .gitignore
    ├── requirements.txt
    ├── questions.md
    ├── (other files that you think are necessary)
    ```
8.  submit your forked repository link to canvas.

## Getting Started (`getting_started.py`)

Before the lab begins, you must create the dataset. We will study the data from an automated plant watering system.

 Use the following script to generate the `plant_sensors.csv` file you will use for the entire lab. 

**Note:** This is not a very realistic simulation of soil moisture over time, the goal is for you to have some basic data to work with.

```python
# Script to generate plant_sensors.csv
import pandas as pd
import numpy as np

# --- SET THE SEED FOR REPRODUCIBILITY ---
np.random.seed(42)
# ----------------------------------------

# We create data hourly over a month
time_range = pd.to_datetime(pd.date_range(start='2023-07-01', end='2023-07-31', freq='H'))

# Properties: 5 plants, in 3 locations
sensors = {
    'A-1': {'plant_type': 'Fiddle Leaf Fig', 'location': 'Living Room'},
    'A-2': {'plant_type': 'Monstera', 'location': 'Living Room'},
    'B-1': {'plant_type': 'Snake Plant', 'location': 'Office'},
    'B-2': {'plant_type': 'Pothos', 'location': 'Office'},
    'C-1': {'plant_type': 'Tomato', 'location': 'Patio'},
}

num_records = len(time_range) * len(sensors)
data = []

for sensor_id, props in sensors.items():
    for ts in time_range:
        # Simulate temperature and light
        hour_of_day = ts.hour
        base_temp = 20 + 5 * np.sin(np.pi * (hour_of_day-8)/12) # Temperature is highest in the afternoon
        temp_c = base_temp + np.random.randn() * 0.5
        
        light = 800 + 600 * np.sin(np.pi * (hour_of_day-6)/14) # Light peaks at noon
        light_level = max(0, light + np.random.randn() * 50)
        
        # Simulate soil moisture with drying and occasional watering
        # Naive model where the soil dries faster in higher temperatures/sunlight, and dires during the day with occasional rewatering at night
        moisture_decay = 1 + temp_c/20 + light_level/800
        base_moisture = 55 - 20 * np.sin(np.pi * (hour_of_day-2)/24) - moisture_decay
        soil_moisture = base_moisture + np.random.randn() * 3

        # Add some sensor errors and NaNs
        if np.random.rand() < 0.02: # 2% chance of a NaN reading
            soil_moisture = np.nan
        elif np.random.rand() < 0.005: # 0.5% chance of complete data corruption
            ts = np.nan
            sensor_id = np.nan
            temp_c = np.nan
            soil_moisture = np.nan
            light_level = np.nan
        elif sensor_id == 'B-1' and np.random.rand() < 0.1: # Sensor B-1 is faulty, 10% chances to have a very high value
             soil_moisture = 95.0 + np.random.randn() 
        
        pump_active = 1 if soil_moisture < 35 and not np.isnan(soil_moisture) else 0

        data.append({
            'timestamp': ts,
            'sensor_id': sensor_id,
            'plant_type': props['plant_type'],
            'location': props['location'],
            'temperature_c': round(temp_c, 2),
            'soil_moisture': round(soil_moisture, 2),
            'light_level': round(light_level, 2),
            'pump_active': pump_active
        })

df = pd.DataFrame(data)
df.to_csv('plant_sensors.csv', index=False)
print("plant_sensors.csv successfully created!")
```

The first step is to check whether the data somewhat corresponds to what we expect. Inspect the data using the command line or a python script and answer the following questions:

**Question 1:** How many rows were generated? How many were expected? Why?

**Question 2:** What appears to be the unit of temperature, soil_moisture, and light_level?



## Part 1: NumPy (`part1.py`)

Be careful in this section to follow EXACTLY the instructions. We will switch between different arrays and even different portions of these arrays!

Reminder: the Numpy documentation is your best friend! https://numpy.org/doc/stable/index.html 

### Exercise 1.1: Array Basics
1.  Load `plant_sensors.csv` just for this step and create a NumPy array named `moisture_readings` from the `soil_moisture` column. (hint: the numpy documentation probably has a function for this...)
2.  A calibration error is found, and all moisture readings need to be decreased by `0.5`. Create a new array `calibrated_moisture` by subtracting 0.5 from `moisture_readings`.
3.  Print the first 5 elements of `calibrated_moisture`.

### Exercise 1.2: Array Slicing and Stats
1.  Using the `moisture_readings` array from 1.1:
2.  Select and print the readings from index 50 to 59 (inclusive).
3.  Calculate and print the summary statistics (`mean`, `median`, `quartile` and `standard deviation`) of **all** the moisture readings, ignoring any `NaN` values (hint: you can find how to exclude nan values in the numpy documentation again...).
4. **Question 3:** What is the difference between the mean and median moisture reading? Given the system's purpose, why might you expect these values to differ?

### Exercise 1.3: Boolean Indexing and Logic
1.  Using the `calibrated_moisture` array:
2.  Create a boolean mask to identify `True` where moisture is less than 35 (the watering threshold). Be careful with `NaN` values.
3.  Use this mask to create a new array `dry_readings` containing only those values.
4.  Print the total number of times a `dry_reading` was recorded.
5.  Use `np.where()` to create a new array `moisture_status`. It should contain the string `'Dry'` for readings < 35, `'Wet'` for readings > 70, and `'OK'` for everything in between. Print a sample of 10 values from this new array.



## Part 2: Pandas Data nalysis (`part2.py`)

In this part, you will clean the raw sensor data, engineer new features, and perform aggregations to answer technical questions.

### Exercise 2.1 (Easy): Initial Inspection & Cleaning
1.  Load `plant_sensors.csv` into a Pandas DataFrame.
2.  Use `.head()`, `.shape`, and `.info()` to get a first look at the data.
3.  The `timestamp` column is an `object`. Convert it to the proper `datetime` type (hint: you guessed it! it is somewhere in the pandas documentation!).
4.  You are working with a friend in the USA. Create a new column `temperature_f` that converts `temperature_c` to Fahrenheit. The formula is `(C * 9/5) + 32`. Or, you can try to find a python library that does it...
5.  **Question 4:** After converting the `timestamp` column, what methods are now available on `df['timestamp'].dt`? (e.g., try `df['timestamp'].dt.hour`).

### Exercise 2.2: Missing Data & Filtering
1.  Use `.isnull().sum()` to identify which column has missing values and how many.
2.  Choose a strategy to handle the missing `soil_moisture` data. `df.interpolate()` is a good choice for time-series data, but requires a bit of configuration. Find this function in the pandas documentation, and set the parameters that you think make the most sense.
3.  Proceed with all other columns that have missing value. Note: `interpolate` may not always be the best choice...
4. **Question 5:** Justify the reason behind your choice.
5.  Filter the DataFrame to create `patio_high_light`, which contains only readings from the 'Patio' where `light_level` was greater than 1200. Display the first 5 rows.

### Exercise 2.3: Grouping and Aggregation
1.  Use `groupby()` to find the average `soil_moisture` for each `sensor_id`.
2.  Use `groupby()` on `plant_type` to find the total number of times the pump was activated (`pump_active.sum()`).
3.  **Advanced Grouping:** Find the `max` `temperature_c` recorded for each of the three locations ('Living Room', 'Office', 'Patio').
4.  **Question 6:** Which sensor is in the driest environment on average? Which plant type requires the most water (has the most pump activations)?


## Part 3: Matplotlib Visualization & Synthesis (`part3.py`)

It is now time to visualise the sensor data to see trends and detect anomalies.

**Note:** Do not hesitate to customize your graph for readability! Graphes that are difficult to read will loose point while very readable graphes will gain some.

### Exercise 3.1: Bar Chart
1.  Using the data you generated in Exercise 2.3 (average moisture per sensor), create a bar chart to visualize this information.
2.  Make sure your plot has a clear title (`Average Soil Moisture by Sensor`), a y-axis label (`Average Moisture (%)`), and that the sensor IDs are readable.

### Exercise 3.2: Line Plot
1.  First, filter your main DataFrame `df` to contain data for **only one sensor** (e.g., `'A-1'`). Set the `timestamp` column as the index of this new DataFrame (`.set_index('timestamp')`).
2.  Create a line plot of the `soil_moisture` for this single sensor over time.
3.  Customize your plot with an appropriate title (`Moisture Level for Sensor A-1`), x-label, and y-label.
4.  **Question 7:** Looking at your line plot, describe the daily pattern of soil moisture. What does it tell you about the environment and the watering system?

### Exercise 3.3: Subplots and Anomaly Detection
1.  Create a Matplotlib figure with two subplots arranged side-by-side (`1 row, 2 columns`). Set an appropriate `figsize` (e.g., `(15, 6)`).
2.  On the left subplot (`axes[0]`): Create a histogram of the `temperature_c` column for all sensors. Use `bins=40`. Give it a title: `Distribution of All Temperature Readings`.
3.  On the right subplot (`axes[1]`): Create a scatter plot comparing `temperature_c` (x-axis) with `soil_moisture` (y-axis). Give it a title: `Temperature vs. Soil Moisture`. Use `alpha=0.1` to make the density of points visible.
4.  **Question 8:** What does the histogram tell you about the temperature environment? Based on the scatter plot, what is the general relationship between temperature and soil moisture? Do you see any unusual patterns or clusters?

## Final Challenge (`final_challenge.py`)

**Objective:** Use your analysis skills to identify a faulty sensor.

**The Scenario:** A technician suspects one of the five sensors is malfunctioning, but isn't sure which one. A faulty sensor might get "stuck" on a value, report nonsensical numbers, or not respond to environmental changes.

**Your Task:**
1.  Perform an analysis to identify the most likely faulty sensor. Your justification should be based on the data only. You can try the following: 
    *   Compare the standard deviation of `soil_moisture` across sensors (a stuck sensor has low deviation).
    *   Look for outliers using `.describe()` on grouped data.
    *   Visualize all sensors on one plot to see which one behaves differently.
    *   Use summary data and other statistical methods to identify the faulty sensor
3.  Create **one visualization** that proves your case. Choose the plot type that best represents the phenomenon you are trying to see. Attach the group in `questions.md`
4.  Write a short paragraph in `questions.md` justifying which sensor you believe is faulty.