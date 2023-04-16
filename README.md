# NETFLIX-Movies-and-Shows
Here I have used Excel, R, SQL, and Tableau to draw insights from data on NETFLIX contents


About the data
Netflix is one of the most popular media and video streaming platforms. They have over 8000 movies or tv shows available on their platform, as of mid-2021, they have over 200M Subscribers globally. This tabular dataset consists of listings of all the movies and tv shows available on Netflix, along with details such as - cast, directors, ratings, release year, duration, etc.

Milestone: Oct 18th, 2021: Most Upvoted Dataset on Kaggle by an Individual Contributor

Objective: 
1.	Understanding what content is available in different countries
2.	Finding similar content by matching text-based features
3.	Network analysis of Actors / Directors and find interesting insights
4.	Does Netflix have more focus on TV Shows than movies in recent years.

Skills used
Data mining, Data manipulation and cleaning, SQL coding, R programming, Desktop design, Data visualization and Data insights.

Main Process
✅ I reviewed the data to find any bad data points, missing data, and duplicated data.  
✅ After that, I performed data cleaning and transformation procedures in Excel. 
•	Duplicate values were detected using conditional formatting. The following contents were duplicated in the data, Love in a Puff, Esperando la carroza, 45153 and 45129

•	The content were categorized as season and minutes using the following excel function =IF(ISNUMBER(FIND("min",J2)),"M",IF(ISNUMBER(FIND("Season",J2)),"S",""))

•	The duration of each content was extracted using =IFERROR(VALUE(LEFT(J2,FIND(" ",J2)-1)),"")

•	And the num_casts column was created using the count the number of cast crew =LEN(E2)-LEN(SUBSTITUTE(E2,",",""))+1

•	The special characters in the title column include #Selfie, #Selfie 69,  #Alive, #AnneFrank - Parallel Stories, #FriendButMarried 2, #FriendButMarried, #realityhigh #Roxy, 3022,2012,122, 2215, 46, 9, 21, 187, 786, 1920, and 1922

•	The following were formatted back as text as 45153, 45129, 45200, 0.999305556, and 0.304861111

•	All the special characters in the director, cast and title section were replaced with alphabets.

•	All cells with blank and null information were removed. Hence the initial number of data 7983 was streamlined to 5333.

•	The last cleaning effort to format fields such as date_added and release_year (YYYY.MM.DD), type, title, director, cast, country, listed_in, and description to text and the remaining fields to numeric.
✅ The final data schema- File type (xlsx/csv), #No of data (5333), #of columns (15).
✅Performed simple Pivot tables
✅ Final step was to divide into the cleaned data and obtain important insights.

Tools
Excel spreadsheet, Power Query, Microsoft PowerBI

Insights from the data
✅ There are more "Movie"	5181(97.24%) than "TV Show" 147 (2.76%) on NETFLIX.
✅ The top 2 countries with the most movies/shows are the United States and India 
✅ The top 3 common rating for movies/shows "TV-MA", "TV-14" and "PG" and with 162, 152 and 80 and these are in the "Stand-Up Comedy", "Comedies, Dramas, International Movies" and genre "Dramas, International Movies" genre and produced from the United States and India.
✅ The average runtime of a movie/show are  110 for "Dramas, Romantic Movies, Thrillers", 
103 for "Comedies, International Movies, Romantic Movies"	, and 158 for "Cult Movies, Dramas, Thrillers".
✅ The top 3 genre are listed below according to Stand-Up Comedy, Documentaries and  Dramas, Independent Movies, International Movies.
✅ The average cast members for "Movie" is 7.97 and 9.33 for "TV Show".
✅ The top 3 countries that have produced the most action & adventure, anime, comedy, international, British TV, Independent, Reality are India (853), United States (766) and United Kingdom (113).
✅ The most common rating for Movie and TV show is TV-14 and goes with Comedies, Dramas, International Movies genre 

Data source
https://www.kaggle.com/datasets/shivamb/netflix-shows 

