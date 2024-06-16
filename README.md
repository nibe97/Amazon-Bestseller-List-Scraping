# Amazon-Bestseller-List-Scraping
Hello, I am an absolute beginner and would like to scrape the Amazon bestseller page. I want a table with the product name, rank, rating, number of ratings, and price. My code works well but only extracts ranks 1-30 and 51-80. I think it's related to the page scrolling. But neither the threads here nor ChatGPT could help me. Any tips?

This is my Code

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from bs4 import BeautifulSoup
import pandas as pd

# Funktion, um die Bestseller von einer Seite zu scrapen
def scrape_page(url):
    driver.get(url)
    # Warten, bis die Bestseller-Elemente geladen sind (mit einer längeren Wartezeit von 30 Sekunden)
    wait = WebDriverWait(driver, 30)
    wait.until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, 'div._cDEzb_iveVideoWrapper_JJ34T')))

    # Inhalt der Seite parsen
    soup = BeautifulSoup(driver.page_source, 'html.parser')

    # Bestseller-Elemente extrahieren
    products = soup.select('div._cDEzb_iveVideoWrapper_JJ34T')

    # Debugging-Ausgabe
    print(f"Anzahl gefundener Produkte auf dieser Seite: {len(products)}")

    for product in products:
        rank = product.select_one('.zg-bdg-text').get_text(strip=True) if product.select_one('.zg-bdg-text') else 'No rank'
        title = product.select_one('div._cDEzb_p13n-sc-css-line-clamp-3_g3dy1').get_text(strip=True) if product.select_one('div._cDEzb_p13n-sc-css-line-clamp-3_g3dy1') else 'No title'
        rating = product.select_one('.a-icon-alt').get_text(strip=True) if product.select_one('.a-icon-alt') else 'No rating'
        reviews = product.select_one('.a-size-small').get_text(strip=True) if product.select_one('.a-size-small') else 'No reviews'
        price = product.select_one('span._cDEzb_p13n-sc-price_3mJ9Z').get_text(strip=True) if product.select_one('span._cDEzb_p13n-sc-price_3mJ9Z') else 'No price'
        
        product_info = [rank, title, rating, reviews, price]
        products_data.append(product_info)

        # Debugging-Ausgabe
        print(f"Extrahierte Informationen für Produkt: {product_info}")

# WebDriver initialisieren
driver = webdriver.Chrome()

# Liste für die gesammelten Daten
products_data = []

# URL der ersten Seite der Bestseller-Liste
base_url = 'https://www.amazon.de/gp/bestsellers/ce-de/17303695031/ref=zg_bs_pg_1_ce-de?ie=UTF8&pg='

# Durch die ersten beiden Seiten navigieren
for page_num in range(1, 3):
    url = base_url + str(page_num)
    scrape_page(url)

# Debugging-Ausgabe
print(f"Gesammelte Daten: {products_data}")

# Daten in ein DataFrame konvertieren
df = pd.DataFrame(products_data, columns=['Rank', 'Title', 'Rating', 'Reviews', 'Price'])

# Ausgabe des DataFrame
print(df)

# Daten in eine CSV-Datei exportieren
df.to_csv('amazon_bestsellers.csv', index=False)

# WebDriver schließen
driver.quit()
