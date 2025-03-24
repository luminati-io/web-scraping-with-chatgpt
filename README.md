# Web Scraping With ChatGPT

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This guide epxlain how to use ChatGPT to generate scripts in Python for web scraping both static and complex websites:

- [Prerequisites](#prerequisites)  
- [Scraping Websites with Static HTML Using ChatGPT](#scraping-websites-with-static-html-using-chatgpt)  
- [Scraping Complex Websites](#scraping-complex-websites)  

## Prerequisites

To follow this guide, you will need to be familiar with Python and have it installed locally, as well as have a ChatGPT account.

When you use ChatGPT to generate your web scraping scripts, there are two main steps:

To create web scraping scripts using ChatGPT, follow these steps:

1. **Identify Target Elements**  
   - Document the steps needed to find and extract data.  
   - Right-click the desired page element → **Inspect** → **Copy > Copy selector** to get the HTML path.  

   ![Copying a selector](https://brightdata.com/wp-content/uploads/2024/07/Copying-a-selector-1.png)

2. **Generate Code with ChatGPT**  
   - Provide clear and detailed prompts specifying the scraping logic.

3. **Execute & Test**  
   - Run the generated script and validate the results.

## Scraping Websites with Static HTML Using ChatGPT

Let’s use ChatGPT to scrape some websites with [static HTML](https://www.w3schools.com/howto/howto_website_static.asp) elements. To start, you’ll scrape the book title and prices from [https://books.toscrape.com](https://books.toscrape.com/).

Start with identifying the HTML elements that contain the data you need.  

* The selector for the book title is `#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > h3 > a`.
* The selector for the book price is \*`#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > div.product_price > p.price_color`.

Next, craft specific and detailed ChatGPT prompts to generate the scraping code. To do this, instruct ChatGPT to install any necessary Python packages, guide it through extracting HTML selectors (which you identified earlier), and request that it saves the data as an Excel file. 

> **Tip**:
> 
> Don’t forget to set the ChatGPT version to GPT-4o.

You can input something like this into ChatGPT:

> You are a web scraping expert utilizing Python’s Beautiful Soup library and any necessary automation tools. I want you to generate a detailed step-by-step script to scrape `https://books.toscrape.com`; kindly do the following:
> 
> 1.  Install any necessary Python packages.
> 2.  Extract the book title, which has the HTML selector `#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > h3 > a`, and the book price (only the number, nothing else), which has the selector `#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > div.product_price > p.price_color`.
> 3.  Extract the list of all book titles and prices and store it in an Excel file.
> 4.  Scrape the first page only.

Please note that you may receive a slightly different response.

In the response, ChatGPT instructs you to run the following command to install the Beautiful Soup library, which performs web scraping, and the [pandas library](https://pandas.pydata.org/), a comprehensive data analysis library that stores data as an Excel:

```bash
pip3 install requests beautifulsoup4 pandas openpyxl
```

You’ll notice that ChatGPT also suggests installing [openpyxl](https://pypi.org/project/openpyxl/), but it’s not needed here.

ChatGPT also provides the following web scraping code:

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL of the website
url = 'https://books.toscrape.com'

# Send a GET request to the website
response = requests.get(url)
if response.status_code == 200:
    # Parse the HTML content of the page
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Initialize lists to store the titles and prices
    book_titles = []
    book_prices = []

    # Extract the first book title and price using the provided selectors
    book_title_selector = "#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > h3 > a"
    book_price_selector = "#default > div.container-fluid.page > div > div > div > section > div:nth-child(2) > ol > li:nth-child(1) > article > div.product_price > p.price_color"

    title_element = soup.select_one(book_title_selector)
    price_element = soup.select_one(book_price_selector)
    
    if title_element and price_element:
        book_titles.append(title_element.get('title'))
        book_prices.append(price_element.text.strip()[1:])

    # Extract all book titles and prices on the first page
    books = soup.select('section > div:nth-child(2) > ol > li')
    for book in books:
        title = book.select_one('h3 > a')
        price = book.select_one('div.product_price > p.price_color')
        if title and price:
            book_titles.append(title.get('title'))
            book_prices.append(price.text.strip()[1:])

    # Create a DataFrame from the lists
    df = pd.DataFrame({
        'Title': book_titles,
        'Price': book_prices
    })

    # Save the DataFrame to an Excel file
    df.to_excel('books_scraped.xlsx', index=False)

    print('Data has been successfully scraped and saved to books_scraped.xlsx')
else:
    print('Failed to retrieve the webpage')
```

This code imports the `requests` module, which handles HTTP calls to scrape website data. It also imports the pandas package to store the scraped data in an Excel file. The code defines the base URL of the website and specifies the HTML selectors used to extract the required data.  

Next, the code retrieves the website content and stores it as a `BeautifulSoup` object named `soup`. Using the identified HTML selectors, it extracts the book titles and prices as lists from the `soup` object. Finally, it creates a pandas DataFrame from these lists and saves the data as an Excel file.  

Next, you need to save the code to a file named `books_scraping.py` and run the command `python3 books_scraping.py` from the command line. This code generates an Excel file named `books_scraped.xlsx` in the same directory as `books_scraping.py`:

![The generated Excel file named books_scraped.xlsx](https://brightdata.com/wp-content/uploads/2024/07/The-generated-Excel-file-named-books_scraped.xlsx-1024x684.png)

Let's scrape another simple website, https://quotes.toscrape.com, which contains a collection of quotes.

Again, let’s start by identifying the sequence of steps.

If you’re using Chrome, find the following HTML selectors:

* `body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span.text` to scrape the **quote**
* `body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span:nth-child(2) > small` to identify the **author**

Once you’ve identified the HTML selectors you want to scrape, draft a detailed prompt to generate the scraping code:

> You are a web scraping expert utilizing Python’s Beautiful Soup library and any necessary automation tools. I want you to generate a detailed step-by-step script to scrape `https://quotes.toscrape.com`. Kindly do the following:
> 
> 1.  Install any necessary Python packages.
> 2.  Extract the quote, which has the HTML selector `body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span.text`, and the author name, which has the selector `body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span:nth-child(2) > small`.
> 3.  Extract the list of all quotes and authors and store it in an Excel file.
> 4.  Scrape the first page only.

After inputting this information, ChatGPT should provide you with the following command, which installs the Beautiful Soup, pandas, and openpyxl libraries:

```bash
pip3 install requests beautifulsoup4 pandas openpyxl
```

ChatGPT should also provide the following web scraping code:

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# Step 1: Fetch the web page content
url = "https://quotes.toscrape.com"
response = requests.get(url)
html_content = response.text

# Step 2: Parse the HTML content using BeautifulSoup
soup = BeautifulSoup(html_content, 'html.parser')

# Step 3: Extract the specific quote and author
quote_selector = "body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span.text"
author_selector = "body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span:nth-child(2) > small"

specific_quote = soup.select_one(quote_selector).get_text()
specific_author = soup.select_one(author_selector).get_text()

print(f"Specific Quote: {specific_quote}")
print(f"Specific Author: {specific_author}")

# Step 4: Extract all quotes and authors
quotes = soup.find_all('div', class_='quote')

all_quotes = []
for quote in quotes:
  text = quote.find('span', class_='text').get_text()
  author = quote.find('small', class_='author').get_text()
  all_quotes.append({"quote": text, "author": author})

# Step 5: Save the quotes to an Excel file
df = pd.DataFrame(all_quotes)
df.to_excel('quotes.xlsx', index=False)

print("Quotes have been saved to quotes.xlsx")
soup = BeautifulSoup(html_content, 'html.parser')

# Step 3: Extract the specific quote and author
quote_selector = "body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span.text"
author_selector = "body > div.container > div:nth-child(2) > div.col-md-8 > div:nth-child(1) > span:nth-child(2) > small"

specific_quote = soup.select_one(quote_selector).get_text()
specific_author = soup.select_one(author_selector).get_text()

print(f"Specific Quote: {specific_quote}")
print(f"Specific Author: {specific_author}")

# Step 4: Extract all quotes and authors
quotes = soup.find_all('div', class_='quote')

all_quotes = []
for quote in quotes:
    text = quote.find('span', class_='text').get_text()
    author = quote.find('small', class_='author').get_text()
    all_quotes.append({"quote": text, "author": author})

# Step 5: Save the quotes to an Excel file
df = pd.DataFrame(all_quotes)
df.to_excel('quotes.xlsx', index=False)

print("Quotes have been saved to quotes.xlsx")
```

Save this code to a file named `quotes_scraping.py` and run the command `python3 books_scraping.py` from the command line. This code generates an Excel file named `quotes_scraped.xlsx` in the same directory as `quotes_scraping.py`. Open the generated Excel file, and it should look like this:

![Generated Excel file with quotes and authors](https://brightdata.com/wp-content/uploads/2024/07/Generated-Excel-file-with-quotes-and-authors-1024x226.png)

## Scraping Complex Websites

Scraping complex websites can be challenging because dynamic content is often loaded via JavaScript, which tools like [`requests`](https://docs.python-requests.org/en/latest/) and `BeautifulSoup` cannot handle. These sites may require interactions such as clicking buttons or scrolling to access all data.  

To overcome this challenge, you can use [WebDriver](https://www.selenium.dev/documentation/webdriver/), which renders pages like a browser and simulates user interactions, ensuring all content is accessible just as it would be for a typical user.  

For example, [Yelp](https://www.yelp.com/) is a crowd-sourced review website for businesses that relies on dynamic page generation and requires multiple user interactions. In this case, you'll use ChatGPT to generate a scraping script that retrieves a list of businesses in Stockholm along with their ratings.  

Here is the workflow for scraping Yelp:

1. Find the selector of the location text box that the script will use; in this case, it’s `#search_location`. Type “Stockholm” in the location search box and then find the search button selector; in this case, it is `#header_find_form > div.y-css-1iy1dwt > button`. Click the search button to see the search results. This may take a few seconds. Find a selector that contains the business name (_ie_ `#main-content > ul > li:nth-child(3) > div.container_\_09f24_\_FeTO6.hoverable_\_09f24_\__UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(1) > div > div > h3 > a`):  

![Getting the selector for the business name](https://brightdata.com/wp-content/uploads/2024/07/Getting-the-selector-for-the-business-name.png)

2. Find the selector that contains the rating for the business (_ie_ `#main-content > ul > li:nth-child(3) > div.container_\_09f24_\_FeTO6.hoverable_\_09f24_\__UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(2) > div > div > div > div.y-css-ohs7lg > span.y-css-jf9frv`):  

![Getting the selector for the business average review](https://brightdata.com/wp-content/uploads/2024/07/Getting-the-selector-for-the-business-average-review.png)

3. Find the selector for the **Open Now** button; here, it’s `#main-content > div.stickyFilterOnSmallScreen_\_09f24_\_UWWJ3.hideFilterOnLargeScreen_\_09f24_\_ilqIP.y-css-9ze9ku > div > div > div > div > div > span > button:nth-child(3) > span`:  
    
![Open Now button selector](https://brightdata.com/wp-content/uploads/2024/07/Open-Now-button-selector.png)
    
4. Save a copy of the web page so that you can upload it later, along with the ChatGPT prompt to help ChatGPT understand the context of the prompts. In Chrome, you can do that by clicking the three dots at the top right and then clicking **Save** and **Share > Save Page As**:  
    
![Save web page in Chrome](https://brightdata.com/wp-content/uploads/2024/07/Save-web-page-in-Chrome.png)    

Next, using the selector values you extracted earlier, you need to draft a detailed prompt to guide ChatGPT in generating the scraping script:

> You are a web scraping expert. I want you to scrape https://www.yelp.com/ to extract specific information. Follow these steps before scraping:
> 
> 1.  Clear the box with the selector `#search_location`.
> 2.  Type “Stockholm” in the search box with the selector `#search_location`.
> 3.  Click the button with the selector `#header_find_form > div.y-css-1iy1dwt > button`.
> 4.  Account for a few seconds for the page to load the search results.
> 5.  Click the button **Open Now**, which has the selector `#main-content > div.stickyFilterOnSmallScreen_\_09f24_\_UWWJ3.hideFilterOnLargeScreen_\_09f24_\_ilqIP.y-css-9ze9ku > div > div > div > div > div > span > button:nth-child(3) > span`, to filter the businesses that are open now.
> 6.  Create a list with business names; the business name has the selector `#main-content > ul > li:nth-child(3) > div.container_\_09f24_\_FeTO6.hoverable_\_09f24_\__UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(1) > div > div > h3 > a`.
> 7.  Create a list with the business reviews; the reviews have the selector `#main-content > ul > li:nth-child(3) > div.container_\_09f24_\_FeTO6.hoverable_\_09f24_\__UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(2) > div > div > div > div.y-css-ohs7lg > span.y-css-jf9frv`.
> 8.  Create an Excel file with the business names and their reviews.
> 9.  Make sure to install all the relevant Python packages.
> 10.  Use WebDriver from Selenium to automate the process.
> 11.  Refer to the attached copy of the results HTML page to give you context about the HTML format.

After inputting this information, ChatGPT tells you to install Selenium, the web automation framework that supports WebDriver, and pandas:

```bash
pip3 install selenium pandas
```

ChatGPT also provides you with some scraping code along with an explanation:

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import pandas as pd
import time

# Initialize the WebDriver
driver = webdriver.Chrome()

try:
    # Open Yelp
    driver.get('https://www.yelp.com/')

    # Clear the location search box
    location_box = driver.find_element(By.CSS_SELECTOR, '#search_location')
    location_box.clear()

    # Enter "Stockholm" in the location search box
    location_box.send_keys('Stockholm')

    # Click the search button
    search_button = driver.find_element(By.CSS_SELECTOR, '#header_find_form > div.y-css-1iy1dwt > button')
    search_button.click()

    # Wait for search results to load
    WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.CSS_SELECTOR, '#main-content'))
    )

    # Click on "Open Now" filter
    open_now_button = driver.find_element(By.CSS_SELECTOR, '#main-content > div.stickyFilterOnSmallScreen__09f24__UWWJ3.hideFilterOnLargeScreen__09f24__ilqIP.y-css-9ze9ku > div > div > div > div > div > span > button:nth-child(3) > span')
    open_now_button.click()

    # Wait for the filter results to load
    time.sleep(5)

    # Extract business names and reviews
    business_names = []
    business_reviews = []

    businesses = driver.find_elements(By.CSS_SELECTOR, '#main-content > ul > li')

    for business in businesses:
        try:
            name = business.find_element(By.CSS_SELECTOR, 'div.container__09f24__FeTO6.hoverable__09f24___UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(1) > div > div > h3 > a').text
            review = business.find_element(By.CSS_SELECTOR, 'div.container__09f24__FeTO6.hoverable__09f24___UXLO.y-css-way87j > div > div.y-css-cxcdjj > div:nth-child(1) > div.y-css-1iy1dwt > div:nth-child(2) > div > div > div > div.y-css-ohs7lg > span.y-css-jf9frv').text
            business_names.append(name)
            business_reviews.append(review)
        except Exception as e:
            print(f"Error extracting data for a business: {e}")

    # Create a DataFrame and save to Excel
    df = pd.DataFrame({'Business Name': business_names, 'Reviews': business_reviews})
    df.to_excel('stockholm_businesses.xlsx', index=False)
    print("Data saved to stockholm_businesses.xlsx")

finally:
    # Close the WebDriver
    driver.quit()

```

Save this script and run it with Python in an IDE like VS Code. You’ll notice that the code launches Chrome, navigates to Yelp, clears the location text box, enters “Stockholm”, clicks the search button, filters businesses that are open now, and then closes the page. After that, the scraping result is saved to the Excel file `stockholm_bussinsess.xlsx`:

![Yelp business reviews in Excel](https://brightdata.com/wp-content/uploads/2024/07/Yelp-business-reviews-in-Excel.png)

All the source code for this tutorial is available on [GitHub](https://github.com/smarter-code/article-chatgpt-webscraping/tree/main).

## Conclusion

While scraping a website like Yelp was straightforward in this guide, in many other scenarios, web scraping complex HTML structures can be challenging.

Bright Data offers a wide variety of data collection services, including advanced proxy services to help bypass IP bans, [Web Unlocker to bypass and solve CAPTCHAs](https://brightdata.com//products/web-unlocker), [Web Scraping APIs](https://brightdata.com/products/web-scraper) for automated data extraction, and a [Scraping Browser](https://brightdata.com/products/scraping-browser) for efficient data extraction.

Start with a free trial today!