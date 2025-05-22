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
def create_temperature_visualisation(df):
    df.plot(x='Date', y=['Max Temp (°C)', 'Min Temp (°C)', 'Avg Temp (°C)'])
    plt.title("Temperature Forecast")
    plt.ylabel("Temperature (°C)")
    plt.xlabel("Date")
    plt.show()

def create_precipitation_visualisation(df):
    df.plot(x='Date', y='Chance of Rain (%)', kind='bar')
    plt.title("Rain Forecast")
    plt.ylabel("Chance of Rain (%)")
    plt.xlabel("Date")
    plt.show()
```

## My Analysis of the Initial Code

The initial code has several issues:

❌ No figure sizing for layout consistency

❌ No grid styling for better readability

❌ No label rotation to prevent text overlap

❌ No annotation of data points or bar heights

❌ No error handling (e.g., empty DataFrame or incorrect column names)

❌ No color or styling customization for aesthetics

❌ No tight_layout() to avoid label cut-off

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
def create_temperature_visualisation(df):
    """
    Plot temperature trends (Max, Min, Average) over time.
    """
    try:
        plt.figure(figsize=(10, 6))
        ax = df.plot(
            x='Date',
            y=['Max Temp (°C)', 'Min Temp (°C)', 'Avg Temp (°C)'],
            marker='o',
            linewidth=2,
            markersize=6
        )
        plt.title("Temperature Forecast", fontsize=14)
        plt.ylabel("Temperature (°C)")
        plt.xlabel("Date")
        plt.xticks(rotation=45)
        plt.grid(True, linestyle='--', alpha=0.6)
        plt.tight_layout()

        # Annotate values
        for line in ax.lines:
            for x, y in zip(line.get_xdata(), line.get_ydata()):
                ax.annotate(f"{y}", (x, y), textcoords="offset points", xytext=(0, 5), ha='center', fontsize=8)

        plt.show()
    except Exception as e:
        print(f"Error in temperature visualization: {e}")

def create_precipitation_visualisation(df):
    """
    Plot precipitation forecast as a bar chart.
    """
    try:
        plt.figure(figsize=(10, 6))
        ax = df.plot(
            x='Date',
            y='Chance of Rain (%)',
            kind='bar',
            color='skyblue',
            legend=False
        )
        plt.title("Rain Forecast", fontsize=14)
        plt.ylabel("Chance of Rain (%)")
        plt.xlabel("Date")
        plt.xticks(rotation=45)
        plt.tight_layout()

        # Annotate bar values
        for i, bar in enumerate(ax.patches):
            height = bar.get_height()
            ax.annotate(f"{height}%", (bar.get_x() + bar.get_width() / 2, height),
                        ha='center', va='bottom', fontsize=8)

        plt.show()
    except Exception as e:
        print(f"Error in precipitation visualization: {e}")
```

## Why My Prompting Strategy Was Effective

1. My follow-up prompt was effective because:
2. Adds clear labels and annotations, improving interpretability.
3. Uses custom styling and colors, enhancing visual appeal.
4. Adds error handling to gracefully manage exceptions.
5. Applies tight layout and text rotation for a cleaner display.
6. Explicitly configures figure size for consistent presentation in GUIs or notebooks.


This approach guided the AI to generate a much more robust function that:

- Is properly documented
- Validates inputs
- Has comprehensive error handling
- Returns structured, processed data instead of raw API response
- Includes the new forecast functionality I requested

The key to effective prompting was being specific about what needed improvement and why, rather than making vague requests for "better" code.
