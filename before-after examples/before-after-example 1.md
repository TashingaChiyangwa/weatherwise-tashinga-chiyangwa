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
def get_weather_data(location):
    url = f"https://wttr.in/{location}?format=j1"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        return None
```

## My Analysis of the Initial Code

The initial code has several issues:

1. Assumes the location input is always valid
2. Returns raw JSON without any option to convert it to a DataFrame
3. Lacks error messages or fallbacks
4. Doesn’t check or restrict the number of forecast days
5. Uses the default requests user-agent, which might be blocked by wttr.in

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
def get_weather_data(location, forecast_days=3, as_dataframe=False):
    """
    Retrieve weather forecast data from wttr.in in JSON format.

    Parameters:
    - location (str): The location to query (e.g., 'London', 'New York').
    - forecast_days (int): Number of forecast days to return (1-3 recommended).
    - as_dataframe (bool): Return data as a pandas DataFrame if True.

    Returns:
    - dict or pd.DataFrame: Forecast weather data for the location.
    """
    if not location or not isinstance(location, str):
        raise ValueError("Location must be a non-empty string.")
    
    if forecast_days not in [1, 2, 3]:
        print("Note: wttr.in supports only up to 3 days of forecast.")
        forecast_days = min(max(forecast_days, 1), 3)

    url = f"https://wttr.in/{location}?format=j1"
    headers = {
        "User-Agent": "Mozilla/5.0 (WeatherApp/1.0)"
    }

    try:
        response = requests.get(url, headers=headers, timeout=10)
        response.raise_for_status()
        data = response.json()

        if as_dataframe:
            # Extract relevant fields
            weather_list = data.get("weather", [])[:forecast_days]
            records = []
            for day in weather_list:
                date = day.get("date", "N/A")
                avg_temp = day.get("avgtempC", "N/A")
                max_temp = day.get("maxtempC", "N/A")
                min_temp = day.get("mintempC", "N/A")
                rain_chance = day.get("hourly", [{}])[0].get("chanceofrain", "N/A")

                records.append({
                    "Date": date,
                    "Average Temp (°C)": avg_temp,
                    "Max Temp (°C)": max_temp,
                    "Min Temp (°C)": min_temp,
                    "Chance of Rain (%)": rain_chance
                })

            return pd.DataFrame(records)

        return data

    except requests.exceptions.RequestException as req_err:
        print(f"Request error: {req_err}")
    except ValueError as json_err:
        print(f"JSON decoding error: {json_err}")
    except Exception as e:
        print(f"Unexpected error: {e}")

    return None
```

## Why My Prompting Strategy Was Effective

My follow-up prompt was effective because:

✅ Robust Input Handling
Validates location input to ensure it's a non-empty string.

Prevents crashes from invalid input types (e.g., integers or None).

✅ Forecast Limit Enforcement
Checks that forecast_days is within the supported range (1–3).

Automatically adjusts out-of-range values and warns the user.

✅ Flexible Output Format
Supports optional conversion to a Pandas DataFrame (as_dataframe=True).

Allows seamless use in data science workflows or visualization tools.

✅ Clean, Structured Data Extraction
Extracts only relevant fields (e.g., date,




This approach guided the AI to generate a much more robust function that:

- Is properly documented
- Validates inputs
- Has comprehensive error handling
- Returns structured, processed data instead of raw API response
- Includes the new forecast functionality I requested

The key to effective prompting was being specific about what needed improvement and why, rather than making vague requests for "better" code.
