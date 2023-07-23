# Insights Provided by A dataset of Video Game Reviews.
This is an insight into the data collected from Metacritic regarding video game reviews, both by Metacritic, and Metacritic users. By analysing this dataset, I will be able to identify market trends over time and gain insight into how the market has changed. Furthermore, it will provide insight into the consumer assessment of video games and may guide developers into delivering games with higher ratings.

[Here is a summary of terms used throughout this publication.](https://github.com/DougWicker/VideoGameCriticReview/blob/main/SummaryofTerms.md)

### Potential Insights
Below is a list of potential insights that could be satisfied by reviewing this dataset:

#### A. Comparison of average Metascore (approved critic score) against User Score.
How do user scores compare to Metascores (critic scores) for different games? Are there any notable differences between the two, and do certain genres or platforms show greater divergence in user and critic ratings?  
Data required - Game name, Metascore, User Score.

#### B. Comparison of Average Scores across Platforms:
How do the average review scores across different gaming platforms and platform types (i.e. handheld consoles, home consoles and PC) compare?  
Data required - Game name, Metascore, User Score, Platform, Platform Type.

#### C. Platform Reception for Multi-Platform Games:
For games that are released on multiple platforms, which platform has the highest average rating, and how does this analysis shed light on platform preferences among players and the impact of different platforms on game performance and reception?  
Data required - Game name (games that are released on multiple platforms), Metascore, User Score, Platform, Platform Type.

#### D. Evolution of Review Scores over Time:
How does the correlation between average review scores over time and the number of games released over time provide valuable insights to identify trends, patterns, and potential factors influencing game reception in the gaming industry?  
Data required - Game name, Metascore, User Score, Release Date.

#### E. Impact of Release Date on Review Scores:
Is there a correlation between the release date of a game and its review scores? Do games released during specific time periods tend to receive higher or lower review scores?  
Data required - Game name, Metascore, User Score, Release Date.

#### F. Genre Popularity from Game Descriptions:
How can the analysis of video game descriptions using natural language processing techniques help identify the most popular genres among players? What valuable insights into the preferences and interests of gamers can be obtained by extracting key themes and characteristics from these descriptions? Which game genres tend to receive the highest review scores on average? Are there any particular genres that consistently perform well or poorly in terms of critical and user reception?  
Data required - Game name, Description (to determine genre using natural language processing), Metascore, User Score, Release Date.

#### G. Platform-Specific Game Preferences:
Are there platform-specific trends in game preferences among users? Do certain platforms attract different types of games, and do these games perform differently in terms of review scores?  
Data required - Game name, Description (to determine genre using natural language processing), Metascore, User Score, Release Date.

### Data Collection
I would like to show my appreciation to Henry Luin for their scraping script used in this project. This script allowed me to pull the relevant data from Metacritic into a pandas dataframe. [Here is a link to Henry's Python scraper script.](https://github.com/henrylin03/video-games/blob/main/scraper.py)  
This script pulled the following:
|Field Value |Field Type |
|:-------------|:------------|
| Name | String |
| Platform | String |
| Release Date | String |
| Summary | String |
| Metascore | Integer |
| User Score | Float |

### Data Preparation & Cleaning
I started by creating a new PostgreSQL database using pgAdmin:  
```SQL
CREATE DATABASE video_game_reviewsdb;  
```

Then I created a table named video_game_reviews with column names based on the scraped data:  
```SQL
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
```SQL
SELECT(*)
FROM video_game_reviews,
ORDER BY idn ASC
LIMIT 10
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20First%2010%20Records.png)
  
The below demonstrates the total number of records held within the table:  
```SQL
SELECT COUNT(*)
FROM video_game_reviews;
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20Number%20of%20Records.png)

  
The below demonstrates the total number of records held within the table grouped by their release platform:  
```SQL
SELECT platform, COUNT(*)
FROM video_game_reviews
GROUP BY platform;
```
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/a0c04ef02e09d44b58a934428109f7361c2ee320/Images/Pre-Cleaning%20by%20Platform.png)

#### Data Cleaning

To start with, I can remove both IOS games and Stadia games as I am only interested in assessing games for home video consoles, handheld consoles & PC.
```SQL
DELETE FROM video_game_reviews
WHERE platform IN ('iOS', 'Stadia');
```

I also want to remove any records that have neither a Metascore nor a User Score:
```SQL
DELETE FROM video_game_reviews
WHERE metascore = 'tbd' AND user_score = 'tbd';
```

I also want to assign a Platform Type to each of the consoles. For example, the PS2, PS3, Xbox One etc. are home video consoles, whilst the Nintendo DS, 3DS & Switch are handheld consoles.  
```SQL
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
```SQL
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
```SQL
UPDATE video_game_reviews
SET release_date = TO_DATE(release_date, 'Month DD, YYYY');

ALTER TABLE video_game_reviews
ALTER COLUMN release_date TYPE DATE USING release_date::DATE;
```

Next, extract the release year as another field from the release date
```SQL
ALTER TABLE video_game_reviews
ADD COLUMN release_year INTEGER;

UPDATE video_game_reviews
SET release_year = EXTRACT(YEAR FROM release_date);
```

#### Summary of Cleaned Data
Now that the data has been cleaned, we can get some headline figures:

The total number of records post-clean:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Total.png)

Below are the top 20 video games ordered by Metascore:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Top%2020%20by%20metascore.png)

Below are the top 20 video games ordered by their user score:
![image](https://github.com/TupperwareBox/VideoGameCriticReview/blob/8044269e9756ed0daac9e860e8320f638ff5b8cf/Images/Cleaned%20Top%2020%20by%20user%20score.png)

### Data Exploration

#### Insight A: How has the average rating changed over time vs number of games released/reviewed?
For this insight, I need to create a table that shows the mean scores for each release year as well as the number of video games released:
```SQL
CREATE TABLE A1_mean_user_scores_by_release_year AS
SELECT release_year, AVG(user_score) As mean_user_score, AVG(metascore) / 10 As mean_metascore, COUNT(idn) As count_releases
FROM video_game_reviews
GROUP BY release_year
ORDER BY release_year ASC;
```
#### Insight B: Platform Comparison for Games Released on Multiple Systems


### Analysis & Insights

#### Average Ratings by Console Type & Console.
The overall average ratings can be determined:
```python
user_score_mean = data['user_score'].mean()
metascore_mean = data['metascore'].mean()
totalcount = data['idn'].count()
```
There is a total of 39011 video game reviews post cleaning.
The mean of all the user score records is 6.75 / 10.
The mean of all the Metascore records is 69.7 / 10.
This demonstrates that the average score given by Metascore is 3.2% higher than the average score given by the users.

A scatter graph depicting the Metascore vs the user score for each game review can be drafted using matplotlib.

```python
sns.set(style = "ticks")
sns.scatterplot(
    data=data,
    x="user_score",
    y="metascore",
    marker="x",
    color="#FF3D36",
    alpha=0.07,
    s=30,
)
sns.regplot(
    data=data, 
    x="user_score", 
    y="metascore", 
    scatter=False, 
    line_kws={"color": "#595959"},
)

totalaveragemetascore = data['metascore'].mean()
totalaverageuser_score = data['user_score'].mean()

plt.axvline(totalaverageuser_score, color='blue', label='Average User Score')
plt.axhline(totalaveragemetascore, color='green', label='Average Metascore')

plt.legend()
plt.show()
```
![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/ea0b2365-42ed-43b1-8a1a-7311e037d3d4)

The same can be done for each platform type:  
![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/8b6854c8-2114-4a33-a688-8fc54fd3b703)
![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/bec84775-0707-496a-9d82-3f2ecca0593d)
![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/247552af-e2ce-44d7-91c3-7ee60446a4b0)

We can determine how the average scores for each platform type compare.

|Platform Type|Total Number of Reviews |Average Metascore |Average User Score |
|-------------:|:------------:|:------------:|:------------:|
|Home Consoles | 17709 | 69.4 | 6.70 |
|Handheld Consoles | 7408 | 68.7 | **6.94** |
|PC | 13894 | **70.9** | 6.73 |
|**Total**|**39011**|**69.7**|**6.75**|  

This tells us that PC game reviews have the highest average Metascore whilst Handheld Console game reviews have the highest average User Score.

Below is a boxplot that demonstrates the spread of User Scores by platform ordered by the mean score.

![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/fe8bff9a-c22e-432a-a9b3-8314283b23ef)

The platforms with the highest median User Scores are Dreamcast (8.0), Playstation 2 (7.9), Playstation (7.85) and Nintendo 64 (7.8). Whilst those with the lowest median User Scores are Playstation 4 (6.4), Playstation 5 (6.8) and Xbox One (6.8).
Points to talk about - age difference of platforms (rose tinted glasses?) - number of games / reviews per platform - iqr of old platforms vs new, could this be because developing and selling games is more accecssbile (more inexperienced developers)

Below is a line / bar graph combo showing the change in average review score for each release year.

![image](https://github.com/DougWicker/VideoGameCriticReview/assets/134697309/4c50f69b-ada4-4076-9b05-e1e0d31676f4)


The average User Score reached its peak in 1999, with a rating of 8.2, but has generally declined since then. In contrast, the average Metascore peaked in 1996 at 8.4 before experiencing a decline to 6.5 in 2007. However, the Metascore has been steadily increasing over the years, reaching 7.3 in 2022 and 7.4 in 2023 (so far). The significant drop in average scores observed in the year 2000 could be attributed to various factors, including the transition to new console generations (PS2 and Dreamcast), the rise of online multiplayer games, and potential challenges in quality control due to the fast-paced evolution of the game development industry at the time.

These trends indicate a possible correlation between the high average review scores of the late 90s and the relatively low number of video game releases during that period. The high review scores could be attributed to either the presence of more specialized professionals among developers who created superior games or the limited selection of video games, leading to a heightened perception of their quality. Additionally, the retrospective nature of reviews for games released before Metacritic's founding in 2001 introduces the possibility of a "rose-tinted glasses" effect, where nostalgic sentiments may have influenced perceptions of game quality.

The upward trend in average Metascore ratings since 2021 may be indicative of an overall improvement in game quality and critical reception. This could be a result of advancements in technology and game development practices, leading to more polished and refined gaming experiences. However, there appears to be no upward trend in the average User Score ratings since 2021. Alternatively, the increasing popularity of remastered games could contribute to the higher average Metascores, as these releases often enhance and improve upon existing high-scoring titles, garnering positive reviews from both critics and players. However, it is important to consider other factors such as changes in review methodologies or shifts in consumer preferences that could also influence the trend.
 
#### Insight 1: Average Rating over Time vs. Number of Games Released/Reviewed
Plot the average rating of video games over time.  
Analyze the relationship between the average rating and the number of games released/reviewed.  
Identify any trends or patterns and provide insights into the changing landscape of video game ratings.  
#### Insight 2: Platform Comparison for Games Released on Multiple Systems
Identify games that have been released on multiple platforms.  
Calculate the average rating for each platform.  
Determine which platform has the highest average rating for these multi-platform games.  
Discuss possible reasons for the observed differences.  

|Game|Platform |Average User Score |
|-------------:|:------------:|:------------:|
|Game 1 | Xbox | 69.4 |
|Game 1 | PC| 34.4 |
|Game 2 | PC | 68.7 | 
|Game 3 | PS3 |70.9 |
|Game 3 | Xbox |74.0 |

|Game|Xbox |PS3 |PC|
|------------:|:----------:|:------------:|:------------:|
|Game 1 | 69.4 | NULL | 34.4|
|Game 2 | Null |Null | 68.7 | 
|Game 3 | 74.0 |70.9 |Null|


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
