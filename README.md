# Automatic-News-Scrapper

A Python project to scrape the Indian Express website's business section, extract news article details, and store them in an SQL database.

## Table of Contents

- [Project Description](#project-description)

- [Installation](#installation)

- [Usage](#usage)

- [Code Explanation](#code-explanation)

- [Contributing](#contributing)

- [License](#license)

- [Contact](#contact)

## Project Description

This project scrapes the business section of the Indian Express website to extract news articles, including the title, content, date, and author of each article. The scraped data is then stored in an SQL database for further analysis or usage.

## Installation

1\. Clone the repository:

    ```bash

    git clone https://github.com/yourusername/indian-express-business-scraper.git

    cd indian-express-business-scraper

    ```

2\. Create a virtual environment and activate it (optional but recommended):

    ```bash

    python3 -m venv venv

    source venv/bin/activate  # On Windows use `venv\Scripts\activate`

    ```

3\. Install the required dependencies:

    ```bash

    pip install -r requirements.txt

    ```

## Usage

1\. Run the scraper script:

    ```bash

    python updated_task_2_aayush.py

    ```

    This will scrape the Indian Express business section and store the articles in the specified SQL database.

## Code Explanation

### Scraper Script (`updated_task_2_aayush.py`)

The script `updated_task_2_aayush.py` performs the following tasks:

1\. **Imports necessary libraries**: Libraries for web scraping (like BeautifulSoup and requests), and SQL database connection (like sqlite3 or SQLAlchemy).

2\. **Defines the base URL**: Sets the base URL for the Indian Express business section.

3\. **Scrapes multiple pages**: Loops through multiple pages to collect article links.

4\. **Extracts article details**: For each article link, it extracts the title, content, date, and author.

5\. **Stores data in a DataFrame**: Uses pandas to store the data.

6\. **Saves data to an SQL database**: Connects to an SQL database and stores the data in a specified table.

### Example Code

Here's a brief snippet of the code for illustration:

```python

import pandas as pd

import requests

from bs4 import BeautifulSoup

from time import sleep

import sqlite3

# base url

url = 'https://indianexpress.com/section/business/'

page = requests.get(url)

soup1 = BeautifulSoup(page.content, 'html.parser')

soup2 = BeautifulSoup(soup1.prettify(), 'html.parser')

container = soup2.find('div', class_='articles')

all_links = []

for i in range(1, 5):

    try:

        url = f"https://indianexpress.com/section/business/page/{i}/"

        page = requests.get(url)

        page.raise_for_status()

        sleep(0.05)

        soup1 = BeautifulSoup(page.content, "html.parser")

        soup2 = BeautifulSoup(soup1.prettify(), "html.parser")

        container = soup2.find_all("div", class_="articles")

        for item in container:

            for a in item.find_all('a', href=True):

                all_links.append(a['href'])

    except Exception as e:

        print(f"Error: {e}")

# Initialize lists to hold the data

titles = []

contents = []

dates = []

authors = []

# Extract data from each article

for link in all_links:

    try:

        article_page = requests.get(link)

        article_soup = BeautifulSoup(article_page.content, 'html.parser')

        title = article_soup.find('h1', class_='native_story_title').get_text()

        content = article_soup.find('div', class_='full-details').get_text()

        date = article_soup.find('div', class_='story-date').get_text()

        author = article_soup.find('a', class_='author').get_text()

        titles.append(title)

        contents.append(content)

        dates.append(date)

        authors.append(author)

    except Exception as e:

        print(f"Error: {e}")

# Create a DataFrame

df = pd.DataFrame({

    'Title': titles,

    'Content': contents,

    'Date': dates,

    'Author': authors

})

# Save DataFrame to an SQL database

conn = sqlite3.connect('news.db')

df.to_sql('business_news', conn, if_exists='replace', index=False)

conn.close()
