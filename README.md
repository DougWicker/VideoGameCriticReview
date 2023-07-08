# Insights Provided by A dataset of Video Game Reviews.
This is an insight into the data collected from Metacritic regarding video game reviews, both by Metacritic, and Metacritic users. By analysing this dataset, I will be able to identify market trends over time and gain insight into how the market has changed. Furthermore, it will provide insight into the consumer assessment of video games and may guide developers into delivering games with higher ratings.

### Potential Insights
Below is a list of potential insights that could be satisfied by reviewing this dataset:

1. How has the average rating changed over time vs number of games released/reviewed?
2. For games that are released on multiple systems, which system has the highest average rating?
3. Using Sentiment Analysis, what game summary is most common? What game summary gets the best reviews?
4. Can I add other video game review sources to compare?
5. Which system has the highest user / meta score ratio?

### Data Collection
I would like to show my appreciation to Henry Luin for their scraping script used in this project. This script allowed me to pull the relevant data from Metacritic into a pandas dataframe. A link to Henry's Python scraper script is below:  
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
I started by creating a new PostgreSQL database using pgAdmin:  
```
CREATE DATABASE video_game_reviewsdb;  
```

Then I created a table named video_game_reviews with column names based on the scraped data:  
```
CREATE TABLE video_game_reviews (
  idn INT,
  game_name VARCHAR(150),
	platform VARCHAR(50),
	release_date VARCHAR(50),
	summary TEXT,
	metascore VARCHAR(50),
	user_score VARCHAR(50)
);  
```
You will notice that release_date, Metascore & user_score are currently all the variable-character length data type. This is because release_date needs to be converted to a date data type and metascore and user_score have 'tbd' values that need to be converted / removed.  

Next, I imported the CSV file using pgAdmin's import/export feature.  

#### Summary of Table
The below demonstrates the table by limiting the output to the first 10 records ordered by idn:  
```
SELECT(*)
FROM video_game_reviews,
ORDER BY idn ASC
LIMIT 10
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20First%2010%20Records.png)
  
The below demonstrates the total number of records held within the table:  
```
SELECT COUNT(*)
FROM video_game_reviews;
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20Number%20of%20Records.png)

  
The below demonstrates the total number of records held within the table grouped by their release platform:  
```
SELECT platform, COUNT(*)
FROM video_game_reviews
GROUP BY platform;
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20by%20Platform.png)

#### Data Cleaning

To start with, I can remove both IOS games and Stadia games as I am only interested in assessing games for home video consoles, handheld consoles & PC.
```
DELETE FROM video_game_reviews
WHERE platform IN ('iOS', 'Stadia');
```

I also want to remove any records that have neither a Metascore nor a User Score:
```
DELETE FROM video_game_reviews
WHERE metascore = 'tbd' AND user_score = 'tbd';
```

I also want to assign a Platform Type to each of the consoles. For example, the PS2, PS3, Xbox One etc. are home video consoles, whilst the Nintendo DS, 3DS & Switch are handheld consoles.  
```
ALTER TABLE video_game_reviews
ADD COLUMN platform_type VARCHAR(50);

UPDATE video_game_reviews
SET platform_type = CASE
    WHEN platform IN ('Dreamcast', 'GameCube', 'Nintendo 64', 'PlayStation','PlayStation 2', 'PlayStation 3', 'PlayStation 4', 'PlayStation 5', 'Wii', 'Wii U', 'Xbox', 'Xbox 360', 'Xbox One', 'Xbox Series X') THEN 'Home Console'
    WHEN platform IN ('3DS', 'Switch', 'DS', 'Game Boy Advance', 'PlayStation Vita', 'PSP') THEN 'Handheld Console'
	When platform IN ('PC') Then 'PC'
	ELSE 'Other'
END;  
```

I also need to convert any 'tbd' values to Null to allow me to assign the numerical tada types to the Metascore and user_score columns.
```
UPDATE video_game_reviews
SET metascore = NULL
WHERE metascore = 'tbd';

UPDATE video_game_reviews
SET user_score = NULL
WHERE user_score = 'tbd';

ALTER TABLE video_game_reviews
ALTER COLUMN metascore TYPE INT USING metascore::INT;

ALTER TABLE video_game_reviews
ALTER COLUMN user_score TYPE DECIMAL(3, 1) USING user_score::DECIMAL(3,1);
```

Next, the release date column needs to be converted to a Date data type:
```
UPDATE video_game_reviews
SET release_date = TO_DATE(release_date, 'Month DD, YYYY');

ALTER TABLE video_game_reviews
ALTER COLUMN release_date TYPE DATE USING release_date::DATE;
```

#### Summary of Cleaned Data
Now that the data has been cleaned, we can get some headline figures:

The total number of records post-clean:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Total.png)

Below are the top 20 video games ordered by Metascore:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Top%2020%20by%20metascore.png)

Below are the top 20 video games ordered by their user score:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Top%2020%20by%20user%20score.png)

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
