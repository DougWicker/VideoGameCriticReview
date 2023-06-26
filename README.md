# Insights Provided by A dataset of Video Game Reviews.
This is an insight into the data collected from metacritic regarding video game reviews, both by metacritic, and metacritic users. By analysing this dataset, I will be able to identify market trends over time and game an insight on how the market has changes. Furthermore it will provide insight into the consumer assessment of video games and may guide developers into delivering games with higher ratings.

### Data Collection
I would like to show my appreciation to Henry Luin for their scraping script used in this project. This script allowed me to pull the relevant data from metacritic into a pandas dataframe. A link to Henry's Python scraper script is below:
https://github.com/henrylin03/video-games/blob/main/scraper.py
This script pulled the following:
| Field Value | Field Type |
| name | String |
| platform | String |


The dataset sourced from a Kaglle user - Henry Lin and is scraped using a Python (Selenium / BeautifulSoup) script (GitHub - scraper.py), and cleaned using a combination of pandas and sql (GitHub - Jupyter Notebook].
The dataset contains all games on Metacritic.com, including their summaries, Metascores and user scores on all platforms (including legacy platforms).

The Python scraper script:
https://github.com/henrylin03/video-games/blob/main/scraper.py

The Python(pandas) & SQL cleaning script:
https://github.com/henrylin03/video-games/blob/main/analysis.ipynb

Can I replicate this?

### Insights
Below is a list of potenital insights that could me satsfied by reviewing this dataset:

1. How has the average rating changed over time vs number of games released/reviewed?
2. For games that are released on multiple systems, which system has the highest average rating?
3. Using Sentiment Analysis, what game summary is most common? What game summary gets the best reviews?
4. Can I add other video game review sources to compare?
5. Which system has the highest user / meta score ratio?

### Setup
