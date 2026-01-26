# API Data Ingestion Project

> *Extract data from REST APIs*

---

## Project Overview

Build a pipeline to ingest data from public APIs:
- Schedule regular extractions
- Handle pagination and rate limits
- Store in data lake format

---

## Example: Weather API

```python
import requests
import pandas as pd

def extract_weather(city: str) -> dict:
    url = f"https://api.open-meteo.com/v1/forecast"
    params = {
        "latitude": 52.52,
        "longitude": 13.41,
        "current_weather": True
    }
    response = requests.get(url, params=params)
    response.raise_for_status()
    return response.json()

def transform(data: dict) -> pd.DataFrame:
    weather = data["current_weather"]
    return pd.DataFrame([{
        "temperature": weather["temperature"],
        "windspeed": weather["windspeed"],
        "time": weather["time"]
    }])

def load(df: pd.DataFrame, path: str):
    df.to_parquet(path, index=False)

# Run
data = extract_weather("Berlin")
df = transform(data)
load(df, "weather.parquet")
```

---

## Handling Pagination

```python
def extract_all_pages(base_url: str) -> list:
    all_data = []
    page = 1
    while True:
        response = requests.get(f"{base_url}?page={page}")
        data = response.json()
        if not data["results"]:
            break
        all_data.extend(data["results"])
        page += 1
    return all_data
```

---

*Back to: [[Index|Projects Home]]*
