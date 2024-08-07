# Toronto Rentals Scraping

I was interested in the Toronto Rental market but wasn't able to find any datasets online. Having experience with Selenium already, I decided to try my hand at scraping an online rental market using BeautifulSoup. However, The website turned out to be a dynamic interface requiring the use of Selenium as well to interact with various elements on the website. I then used Microsoft Excel to clean all the data before exporting it as a CSV into BigQuery for the next step of my project. The code is as follows:

---

```python
from bs4 import BeautifulSoup
from tqdm import tqdm
from time import sleep
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
import pandas as pd

serv = Service('/usr/local/bin/chromedriver')
driver = webdriver.Chrome(service=serv)

def load_page(city, housing_type, beds, page):
    url = f'https://www.torontorentals.com/{city}/{housing_type}?beds={beds}%2B&p={page}'
    driver.get(url)
    sleep(45)
    page = driver.page_source
    soup = BeautifulSoup(page, "html.parser")
    return soup


listing_address = []
rental_price = []
bed_number = []
bath_number = []
listing_dimensions = []
for page_number in tqdm(range(1, 99)):
    sleep(3)
    result = load_page('toronto', 'condos', '1', page_number)
    for street in result.find_all('div', class_='r-listing-address'):
        sleep(3)
        listing_address.append(street.text.strip())
    for price in result.find_all('a', class_='r-listing-price'):
        sleep(3)
        rental_price.append(price.text.strip())
    for bed in result.find_all('span', class_='r-listing-infos__beds'):
        sleep(3)
        bed_number.append(bed.text.strip())
    for bath in result.find_all('span', class_='r-listing-infos__baths'):
        sleep(3)
        bath_number.append(bath.text.strip())
    dimensions = result.find_all('div', class_='r-listing-infos bold')
    for dimension in dimensions:
        sleep(3)
        if not dimension.find_all('span', class_='r-listing-infos__dimensions'):
            listing_dimensions.append('Empty')
        else:
            listing_dimensions.append(dimension.get_text(strip=True))

rentals_df_condos = pd.DataFrame({'City': 'Toronto', 'Housing Type': 'Condo', 'Address': listing_address, 'Rent': rental_price, 'Beds': bed_number, 'Baths': bath_number, 'Size': listing_dimensions})
rentals_df_condos.to_csv('rentals_df_condos.csv')
```
