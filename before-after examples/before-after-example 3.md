---
title: "Before/After Example: Weather Data Retrieval Function"
author: "Tashinga Chiyangwa"
format: 
  pdf:
    toc: false
    colorlinks: true
  docx:
    toc: false
    highlight-style: github
  html:
    toc: true
    toc-expand: 2
    embed-resources: true
---


## Initial Prompt

```
Write a function to retrieve weather data from the wttr.in API for a given location.
```

## Initial AI-Generated Code (Before)

```python
def weather_assessment(df):
    if df is None or df.empty:
        print("No weather data available.")
        return {}

    warmest_day = df.loc[df['Max Temp (°C)'].idxmax()]
    coldest_day = df.loc[df['Min Temp (°C)'].idxmin()]
    rainiest_day = df.loc[df['Chance of Rain (%)'].idxmax()]

    print("Weather Summary:")
    print(f"Warmest Day: {warmest_day['Date']} - {warmest_day['Max Temp (°C)']}°C")
    print(f"Coldest Day: {coldest_day['Date']} - {coldest_day['Min Temp (°C)']}°C")
    print(f"Rainiest Day: {rainiest_day['Date']} - {rainiest_day['Chance of Rain (%)']}%")

    return {
        "Warmest Day": warmest_day['Date'],
        "Coldest Day": coldest_day['Date'],
        "Rainiest Day": rainiest_day['Date']
    }

```

## My Analysis of the Initial Code

The initial code has several issues:

❌ Data type checks for numeric conversion (e.g., if temp or rain data are strings).

❌ Comfort Index, which adds useful context for deciding the best day for outdoor activities.

❌ Error handling if columns are missing or malformed.

❌ Configurable verbosity (e.g., show_details=True).

❌ Structured return data for easier reuse.

❌ Docstring documentation.



## My Follow-up Prompt

```
Thanks for the starting point. I'd like to improve this function with:

1. Error handling for API failures and bad responses
2. Location input validation
3. A timeout parameter to prevent hanging
4. A way to process the response to return only essential weather information
5. Proper documentation with parameters and return values explained

Also, can you make it handle the forecast_days parameter to allow getting forecast for multiple days?
```

## Improved AI-Generated Code (After)

```python
def weather_assessment(df, show_details=True):
    """
    Analyze a 3- to 5-day weather forecast DataFrame and print insights.

    Parameters:
    - df (pd.DataFrame): DataFrame containing weather data with the columns:
        'Date', 'Max Temp (°C)', 'Min Temp (°C)', 'Average Temp (°C)', 'Chance of Rain (%)'
    - show_details (bool): If True, prints detailed assessment.

    Returns:
    - dict: Summary containing warmest, coldest, and rainiest day information.
    """

    if df is None or df.empty:
        print("Weather data is not available for assessment.")
        return {}

    # Convert relevant columns to numeric (handles any unexpected strings)
    df['Max Temp (°C)'] = pd.to_numeric(df['Max Temp (°C)'], errors='coerce')
    df['Min Temp (°C)'] = pd.to_numeric(df['Min Temp (°C)'], errors='coerce')
    df['Average Temp (°C)'] = pd.to_numeric(df['Average Temp (°C)'], errors='coerce')
    df['Chance of Rain (%)'] = pd.to_numeric(df['Chance of Rain (%)'], errors='coerce')

    # Compute additional metric: Comfort Index (lower rain + moderate temp = better)
    df['Comfort Index'] = (100 - df['Chance of Rain (%)']) - abs(df['Average Temp (°C)'] - 22)

    warmest_day = df.loc[df['Max Temp (°C)'].idxmax()]
    coldest_day = df.loc[df['Min Temp (°C)'].idxmin()]
    rainiest_day = df.loc[df['Chance of Rain (%)'].idxmax()]
    best_day = df.loc[df['Comfort Index'].idxmax()]

    if show_details:
        print("\n  Weather Assessment Summary:")
        print(f"Warmest Day: {warmest_day['Date']} ({warmest_day['Max Temp (°C)']}°C)")
        print(f"Coldest Day: {coldest_day['Date']} ({coldest_day['Min Temp (°C)']}°C)")
        print(f"Rainiest Day: {rainiest_day['Date']} ({rainiest_day['Chance of Rain (%)']}% chance of rain)")
        print(f"Best Day for Outdoor Activity: {best_day['Date']} (Comfort Score: {best_day['Comfort Index']:.2f})")

    return {
        "Warmest Day": {
            "date": warmest_day['Date'],
            "temp": warmest_day['Max Temp (°C)']
        },
        "Coldest Day": {
            "date": coldest_day['Date'],
            "temp": coldest_day['Min Temp (°C)']
        },
        "Rainiest Day": {
            "date": rainiest_day['Date'],
            "rain_chance": rainiest_day['Chance of Rain (%)']
        },
        "Best Day": {
            "date": best_day['Date'],
            "comfort_score": round(best_day['Comfort Index'], 2)
        }
    }

```

## Why My Prompting Strategy Was Effective

My follow-up prompt was effective because:

✅ Data Safety & Validation
Converts columns to numeric using pd.to_numeric() to prevent errors from unexpected string values or missing data.

Checks if the DataFrame is None or empty before proceeding.

✅ Comfort Index Calculation
Introduces a custom metric:
"Comfort Index" = (100 - Chance of Rain) - |Avg Temp - 22|
This provides a more human-friendly recommendation for the best outdoor day.

✅ Detailed and Configurable Output
Uses the show_details=True parameter to control whether to print the analysis—flexible for both script and GUI use.

Displays the best day for outdoor activities based on comfort, which is more useful than raw temperature alone.

✅ Structured and Reusable Return Value
Returns a clean, well-organized dictionary with dates and metrics for warmest, coldest, rainiest, and best day.

Makes the output easy to use in UIs, reports, or visual dashboards.

✅ Clear Documentation
Includes a comprehensive docstring that explains:

1. Purpose of the function

2. Expected input

3. Optional parameters

4. Structure of the output

✅ Readable and Maintainable Code
Uses meaningful variable names like warmest_day and best_day.

Clearly separates logic blocks (conversion, analysis, printing, return).

✅ Robustness and Error Handling
Gracefully handles missing or malformed data rather than failing silently or crashing.

Ensures the function is safe to use in production or user-facing applications.

This approach guided the AI to generate a much more robust function that:

- Is properly documented
- Validates inputs
- Has comprehensive error handling
- Returns structured, processed data instead of raw API response
- Includes the new forecast functionality I requested

The key to effective prompting was being specific about what needed improvement and why, rather than making vague requests for "better" code.
