# Import necessary libraries
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt
from bs4 import BeautifulSoup
import requests

# Function to fetch stock data using yfinance
def fetch_stock_data(ticker):
    stock = yf.Ticker(ticker)
    stock_data = stock.history(period="max")
    return stock_data

# Function to scrape revenue data from MacroTrends
def scrape_revenue_data(url):
    try:
        response = requests.get(url)
        soup = BeautifulSoup(response.content, 'html.parser')
        
        # Find all tables on the page
        tables = soup.find_all('table')
        
        # Check if any tables were found
        if not tables:
            print("No tables found on the page.")
            return pd.DataFrame()

        # Find the first relevant table (may need to adjust based on actual content)
        revenue_table = pd.read_html(str(tables[0]))[0]
        revenue_table.columns = ['Date', 'Revenue']  # Adjust as necessary
        return revenue_table
    except Exception as e:
        print(f"Error while scraping data: {e}")
        return pd.DataFrame()

# Plotting stock vs revenue
def plot_stock_and_revenue(stock_data, revenue_data, title):
    fig, ax1 = plt.subplots()

    # Plot stock data
    ax1.plot(stock_data.index, stock_data['Close'], color='blue', label='Stock Price')
    ax1.set_xlabel('Date')
    ax1.set_ylabel('Stock Price', color='blue')

    # Plot revenue data on second axis
    if not revenue_data.empty:
        ax2 = ax1.twinx()
        ax2.plot(revenue_data['Date'], revenue_data['Revenue'], color='red', label='Revenue')
        ax2.set_ylabel('Revenue', color='red')

    plt.title(title)
    plt.show()

# Fetch and display Tesla stock and revenue data
print("Fetching Tesla Stock Data...")
tesla_stock_data = fetch_stock_data('TSLA')
print(tesla_stock_data.head())

print("Scraping Tesla Revenue Data...")
tesla_revenue_url = 'https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue'
tesla_revenue_data = scrape_revenue_data(tesla_revenue_url)
print(tesla_revenue_data.head())

# Fetch and display GameStop stock and revenue data
print("Fetching GameStop Stock Data...")
gamestop_stock_data = fetch_stock_data('GME')
print(gamestop_stock_data.head())

print("Scraping GameStop Revenue Data...")
gamestop_revenue_url = 'https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue'
gamestop_revenue_data = scrape_revenue_data(gamestop_revenue_url)
print(gamestop_revenue_data.head())

# Plot Tesla Stock and Revenue Dashboard
print("Plotting Tesla Stock and Revenue Dashboard...")
plot_stock_and_revenue(tesla_stock_data, tesla_revenue_data, "Tesla Stock Price and Revenue")

# Plot GameStop Stock and Revenue Dashboard
print("Plotting GameStop Stock and Revenue Dashboard...")
plot_stock_and_revenue(gamestop_stock_data, gamestop_revenue_data, "GameStop Stock Price and Revenue")
