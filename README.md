# Insights Provided by A dataset of Video Game Reviews.
This is an insight into the data collected from metacritic regarding video game reviews, both by metacritic, and metacritic users. By analysing this dataset, I will be able to identify market trends over time and game an insight on how the market has changes. Furthermore it will provide insight into the consumer assessment of video games and may guide developers into delivering games with higher ratings.

### Potential Insights
Below is a list of potenital insights that could me satsfied by reviewing this dataset:

1. How has the average rating changed over time vs number of games released/reviewed?
2. For games that are released on multiple systems, which system has the highest average rating?
3. Using Sentiment Analysis, what game summary is most common? What game summary gets the best reviews?
4. Can I add other video game review sources to compare?
5. Which system has the highest user / meta score ratio?

### Data Collection
I would like to show my appreciation to Henry Luin for their scraping script used in this project. This script allowed me to pull the relevant data from metacritic into a pandas dataframe. A link to Henry's Python scraper script is below:  
https://github.com/henrylin03/video-games/blob/main/scraper.py  
This script pulled the following:
|Field Value |Field Type |
|-------------|------------|
| Name | String |
| Platform | String |
| Release Date | String |
| Summary | String |
| Metascore | Integer |
| User Score | Float |

### Data Preparation & Cleaning

### Analysis & Insights
#### Insight 1: Average Rating over Time vs. Number of Games Released/Reviewed
Plot the average rating of video games over time.  
Analyze the relationship between the average rating and the number of games released/reviewed.  
Identify any trends or patterns and provide insights into the changing landscape of video game ratings.  
#### Insight 2: Platform Comparison for Games Released on Multiple Systems
Identify games that have been released on multiple platforms.  
Calculate the average rating for each platform.  
Determine which platform has the highest average rating for these multi-platform games.  
Discuss possible reasons for the observed differences.  
#### Insight 3: Sentiment Analysis of Game Summaries
Perform sentiment analysis on game summaries using natural language processing techniques.  
Identify the most common sentiment expressed in the game summaries (positive, negative, neutral).  
Determine which sentiment receives the best reviews based on corresponding critic/user scores.  
Discuss any interesting findings and potential implications.  
#### Insight 4: Comparison with Other Video Game Review Sources
Discuss the possibility of adding other video game review sources for comparison.  
Identify suitable additional sources and describe the process of integrating their data.  
Compare the ratings and reviews from different sources and highlight any differences or similarities.  
Provide insights on the consistency or divergence of ratings across platforms.  
#### Insight 5: User/Meta Score Ratio by Platform
Calculate the user score to critic score ratio for each platform.  
Identify the platform with the highest user/meta score ratio.  
Discuss potential reasons for the observed ratio differences among platforms.  

### Conclusion
Summarize the key findings and insights obtained from the data analysis.  
Discuss the implications of the findings for the video game industry.  
Highlight any limitations or areas for further research.  
Conclude by emphasizing the value of analyzing video game reviews on Metacritic.  
### References
Provide a list of sources and references used in the project.  
Remember to adapt and customize this outline to fit your specific project requirements and available data. Good luck with your analysis!  

The Python scraper script:
https://github.com/henrylin03/video-games/blob/main/scraper.py

The Python(pandas) & SQL cleaning script:
https://github.com/henrylin03/video-games/blob/main/analysis.ipynb
