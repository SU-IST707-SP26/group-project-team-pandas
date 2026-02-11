import requests
import json

# 1. Define the API URL
api_url = "https://data.cityofnewyork.us/api/v3/views/43nn-pn8j/query.json"

# 2. Make a GET request
response = requests.get(api_url)

# 3. Check the status code
if response.status_code == 200:
    print("API request successful!")
    # 4. Parse the JSON data into a Python dictionary
    data = response.json()
    
    # 5. Access and print data
    print(f"Name: {data['name']}")
    print("Abilities:")
    for ability in data['abilities']:
        print(f"- {ability['ability']['name']}")

    # 6. Store the data in a JSON file
    with open('ditto_data.json', 'w') as f:
        json.dump(data, f, indent=4)
    print("Data saved to ditto_data.json")

else:
    print(f"Error: API request failed with status code {response.status_code}")

