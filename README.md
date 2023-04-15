# NETFLIX-Movies-and-Shows
Here I have used Excel, R, SQL, and Tableau to draw insights from data on NETFLIX contents

Main ideas 
1.	Understanding what content is available in different countries
2.	Identifying similar content by matching text-based features
3.	Network analysis of Actors / Directors and find interesting insights
4.	Does Netflix has more focus on TV Shows than movies in recent years.

R Code below
#### Load required libraries
library(data.table)
library(dplyr)
library(tidyr)
library(ggplot2)
library(readr)
library(magrittr)
library(tidyverse)
library(ggpubr)

View(netflix_titles)
head(netflix_titles)
summary(netflix_titles)

# What is the distribution of TV shows vs. movies in your dataset?
# count the number and percentage of TV shows and movies
tv_movie_count <- netflix_titles %>%
  group_by(type) %>%
  summarise(count = n()) %>%
  mutate(percent = count/sum(count)*100)

# create a bar plot
ggplot(tv_movie_count, aes(x = type, y = count, fill = type)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("red", "lightblue")) +
  labs(title = "Distribution of TV shows vs. movies", x = "Type", y = "Count")

#Which countries have produced the most movies/shows in your dataset, and how has this changed over time?
# count the number of movies and shows produced by each country in each year
country_count <- netflix_titles %>%
  group_by(country, release_year) %>%
  summarise(count = n()) %>%
  ungroup() %>%
  arrange(desc(count))
# plot the top 10 countries with the highest count of movies/shows
ggplot(head(country_count, n = 10), aes(x = country, y = count, fill = country)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c(rep("blue", 10))) +
  labs(title = "Top 10 Countries by Number of Movies/Shows Produced", x = "Country", y = "Count") +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))

# plot the trend of the number of movies/shows produced over time for a selected country
ggplot(filter(country_count, country == "United States"), aes(x = release_year, y = count)) +
  geom_line() +
  labs(title = "Number of Movies/Shows Produced by the United States Over Time", x = "Year", y = "Count")

# What is the most common rating for movies/shows in your dataset, and how does this vary by country or genre?
# count the number of movies/shows with each rating by country and genre
rating_count <- netflix_titles %>%
  group_by(rating, country, listed_in) %>%
  summarise(count = n()) %>%
  ungroup() %>%
  arrange(desc(count))

# plot the top 10 rated movies/shows by country and genre
ggplot(head(rating_count, n = 10), aes(x = rating, y = count, fill = country)) +
  geom_bar(stat = "identity") + facet_wrap(~listed_in) + scale_fill_manual(values = c(rep("red", 10))) +
  labs(title = "Top 10 Rated Movies/Shows by Country and Genre", x = "Rating", y = "Count")

# plot the top 10 rated movies/shows by country and rating
ggplot(head(rating_count, n = 10), aes(x = rating, y = count, fill = listed_in)) + geom_bar(stat = "identity") + facet_wrap(~country) +  scale_fill_manual(values = c("yellow", "red", "darkblue", "blue", "lightblue", "gray","pink", "cyan", "black"))

#Which directors have the most titles?
director_counts <- netflix_titles %>%
  group_by(director) %>%
  summarize(count = n()) %>%
  arrange(desc(count)) %>%
  top_n(10)

ggplot(director_counts, aes(x = reorder(director, count), y = count)) +
  geom_col(fill = "pink") +
  coord_flip() +
  labs(title = "Directors with the most titles in the dataset", x = "Director", y = "Number of titles")

# Filter out rows with missing values in duration
netflix_titles <- netflix_titles %>%
  filter(!is.na(duration2))

# Group the data by genre and calculate the average runtime
genre_runtime <- netflix_titles %>%
  group_by(type, listed_in) %>%
  summarise(avg_runtime = mean(duration2)) %>%
  arrange(desc(avg_runtime)) %>%
  group_by(type) %>%
  top_n(10, avg_runtime) %>%
  ungroup()

# Create the plot
ggplot(genre_runtime, aes(x = avg_runtime, y = fct_reorder(listed_in, avg_runtime), fill = type)) +
  geom_col(position = "dodge") +
  scale_fill_manual(values = c("#E50914", "#221F1F")) +
  labs(title = "Top 10 Genres by Average Runtime on Netflix",
       x = "Average Runtime (minutes)",
       y = "Genre") +
  theme_minimal()

# Count the number of titles by listed_in and country
titles_by_genre <- netflix_titles %>%
  group_by(listed_in, country) %>%
  summarize(count = n()) %>%
  ungroup()

# Create a stacked bar chart of the number of titles by genre and country
ggplot(titles_by_genre, aes(x = country, y = count, fill = listed_in)) +
  geom_bar(stat = "identity") +
  theme(legend.position = "bottom") +
  labs(title = "Number of Titles by Genre and Country", x = "Country", y = "Count", fill = "Genre")

#How does the number of cast members vary by movie/show type (i.e. movie vs. TV show)?
# Count the number of cast members by type and title
cast_counts <- netflix_titles %>%
  group_by(type, title) %>%
  summarize(num_casts = n()) %>%
  ungroup()

# Average the number of cast members by type
avg_casts_by_type <- netflix_titles %>%
  group_by(type) %>%
  summarize(avg_casts = mean(num_casts))

# Create a bar chart of the average number of cast members by type
ggplot(avg_casts_by_type, aes(x = type, y = avg_casts)) +
  geom_bar(stat = "identity") +
  labs(title = "Average Number of Cast Members by Type",
       x = "Type",
       y = "Average Number of Cast Members")

#What is the distribution of release years for action & adventure, anime, comedy, international, British TV, Independent, Reality movies/shows in your dataset?
# Filter the dataset for the selected genres
selected_genres <- c("Action & Adventure", "Anime", "Comedy", "International", "British TV", "Independent", "Reality")
filtered_netflix <- netflix_titles %>% filter(listed_in %in% selected_genres)

# Group the data by release_year and count the number of titles in each year
netflix_by_genre_year <- filtered_netflix %>% group_by(release_year) %>% summarize(count=n())

# Plot the distribution of release years for each genre
ggplot(netflix_by_genre_year, aes(x=release_year, y=count, color=release_year)) + geom_line() + scale_color_gradient(low="blue", high="red")

# Which actors or actresses have appeared in the most action & adventure, anime, comedy, international, British TV, Independent, Reality?
genres <- c("Action & Adventure", "Anime", "Comedy", "International", "British TV", "Independent", "Reality")
cast_by_genre <- netflix_titles %>%
  filter(str_detect(listed_in, str_c(genres, collapse = "|"))) %>%
  group_by(cast) %>%
  summarize(num_appearances = n()) %>%
  arrange(desc(num_appearances))

cast_by_genre %>%
  top_n(10, num_appearances) %>%
  ggplot(aes(x = reorder(cast, num_appearances), y = num_appearances, fill = cast)) +
  geom_bar(stat = "identity") +
  coord_flip() +
  labs(title = "Top Actors/Actresses by Genre", x = "Actor/Actress", y = "Number of Appearances", fill = "Actor/Actress")

# How does the average runtime of action & adventure, anime, comedy, international, British TV, Independent, Reality compare to the average runtime of movies/shows in general?
# Plot 1: Average Runtime of Movies/Shows in General
avg_runtime_plot <- ggplot(avg_runtime, aes(x = "", y = avg_total_runtime, fill = "Total")) + 
  geom_bar(width = 1, stat = "identity") + 
  geom_text(aes(label = round(avg_total_runtime, 1)), vjust = 1.5, size = 5) +
  coord_polar(theta = "y") +
  ggtitle("Average Runtime of Movies/Shows in General")

# Plot 2: Average Runtime of Action & Adventure, Anime, Comedy, International, British TV, Independent, Reality Movies/Shows
avg_genre_runtime_plot <- ggplot(avg_runtime, aes(x = "", y = avg_genre_runtime, fill = "Genre")) + 
  geom_bar(width = 1, stat = "identity") + 
  geom_text(aes(label = round(avg_genre_runtime, 1)), vjust = 1.5, size = 5) +
  coord_polar(theta = "y") +
  ggtitle("Average Runtime of Action & Adventure, Anime, Comedy, International, British TV, Independent, Reality Movies/Shows")

# Arrange and render the plots
ggarrange(avg_runtime_plot, avg_genre_runtime_plot, ncol = 2)

# What are the most common sub-genres of action & adventure, anime, comedy, international, British TV, Independent, Reality movies/shows, and how do these vary by country or release year?
subgenres <- netflix_titles %>% 
  filter(str_detect(listed_in, "Action & Adventure|Anime|Comedy|International|British TV|Independent|Reality")) %>% 
  separate_rows(listed_in, sep = ", ") %>% 
  group_by(listed_in, country, release_year) %>% 
  summarise(num_titles = n()) %>% 
  ungroup()

subgenres_plot <- subgenres %>% 
  filter(listed_in %in% c("Action & Adventure", "Anime", "Comedy", "International", "British TV", "Independent", "Reality")) %>% 
  ggplot(aes(x = country, y = num_titles, fill = listed_in)) +
  geom_bar(stat = "identity", position = position_dodge()) +
  facet_wrap(~listed_in, scales = "free") +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1)) +
  ggtitle("Number of Titles by Subgenre, Country and Release Year")

subgenres_plot


