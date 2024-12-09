# Apartment Data Processing Project

This project demonstrates the process of cleaning, transforming, and enriching apartment listing data using Python and the Pandas library. The enriched dataset includes geolocation coordinates fetched via the Google Maps API. The final data is exported as a CSV file for further analysis.

## Features

- **Data Loading**: Load and inspect apartment listing data from a CSV file.
- **Data Cleaning**: Remove unnecessary columns and rename existing ones for better clarity.
- **Data Enrichment**: Generate complete addresses and retrieve geolocation coordinates using the Google Maps API.
- **Data Export**: Save the cleaned and enriched dataset into a new CSV file.

## Prerequisites

- Python 3.7 or higher
- Libraries: `pandas`, `googlemaps`
- Google Maps API key

## Getting Started

### 1. Clone the Repository

Clone this repository to your local machine:
```bash
git clone <repository_url>
```

### 2. Install Dependencies

Install the required Python libraries using pip:
```bash
pip install pandas googlemaps
```

### 3. Setup Google Maps API

Obtain a Google Maps API key and replace `'escrevasuaapi'` with your actual API key in the script.

### 4. Run the Script

Execute the Python script to:
- Load the dataset `apartment_ads_final.csv`
- Clean and transform the data
- Fetch geolocation coordinates for each apartment listing
- Export the enriched dataset as `data_final_export.csv`

```bash
python script.py
```

## Code Explanation

### Load Data
```python
import pandas as pd

data_teste = pd.read_csv('apartment_ads_final.csv')
data_teste.head()
```
Load the dataset and preview the first few rows.

### Clean Data
```python
columns_to_drop = ['Title', 'Useful Area', 'Link']
columns_to_drop = [col for col in columns_to_drop if col in data_teste.columns]
data_teste = data_teste.drop(columns=columns_to_drop)
data_teste.rename(columns={
    'Neighborhood': 'bairro',
    'Address': 'endereco',
    'Total Area': 'area',
    'Bedrooms': 'quartos',
    'Bathrooms': 'banheiros',
    'Garage': 'vaga',
    'Rent': 'aluguel',
    'Condo Fee': 'condominio'
}, inplace=True)
data_teste.head()
```
Drop unnecessary columns and rename others to follow a consistent naming convention.

### Enrich Data
```python
data_teste['endereco_completo'] = data_teste.apply(
    lambda row: f"{row['endereco']}, {row['bairro']}, São Paulo, São Paulo, Brasil", axis=1
)
```
Generate a complete address string for each listing.

```python
import googlemaps

gmaps = googlemaps.Client(key='your_api_key')

def get_coordinates(address):
    geocode_result = gmaps.geocode(address)
    if geocode_result:
        location = geocode_result[0]['geometry']['location']
        return (location['lat'], location['lng'])
    else:
        return (None, None)

data_teste['coordenadas'] = data_teste['endereco_completo'].apply(get_coordinates)
data_teste.head()
```
Fetch geolocation coordinates using the Google Maps API.

### Export Data
```python
data_teste.to_csv('data_final_export.csv', index=False)
```
Save the cleaned and enriched dataset as a CSV file.

## Output Example

| bairro        | endereco                        | area | quartos | banheiros | vaga | aluguel   | condominio        | endereco_completo                                                | coordenadas                 |
|---------------|---------------------------------|------|---------|-----------|------|-----------|-------------------|------------------------------------------------------------------|-----------------------------|
| Centro        | Rua Aurora                     | 38.0 | 1.0     | 1.0       | 0    | R$ 1.450  | R$ 576 Condominio | Rua Aurora, Centro, São Paulo, São Paulo, Brasil              | (-23.5390208, -46.6387669) |
| Vila Buarque  | Rua Marquês de Itu            | 31.0 | 1.0     | 1.0       | 1    | R$ 1.459  | R$ 720 Condominio | Rua Marquês de Itu, Vila Buarque, São Paulo, São Paulo, Brasil | (-23.5437626, -46.6498096) |

## Notes

- Ensure that your Google Maps API usage complies with their terms and pricing.
- For large datasets, consider rate limiting to avoid exceeding API quotas.

## License

This project is licensed under the MIT License. See the LICENSE file for details.

---

For questions or contributions, feel free to open an issue or submit a pull request!

