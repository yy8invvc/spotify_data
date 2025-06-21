# Project Category: [Advanced Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](2015-spotify-logo-600x319.png)


# Spotify SQL Data Analysis & Optimization Project

## Project Overview
This advanced SQL project analyzes Spotify's music dataset to extract meaningful insights about tracks, albums, and artists. The project demonstrates comprehensive database skills including schema design, complex querying, and performance optimization.

## Dataset Features
The dataset contains rich information about music tracks, including:
- Artist and track metadata
- Album details and types
- Audio characteristics (danceability, energy, tempo, etc.)
- Engagement metrics (streams, views, likes, comments)
- Video information (official status, channel)

## Technical Approach

### Database Schema Design
Created an optimized table structure to store the diverse Spotify data:

```sql
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);


Progressive SQL Analysis

-- ======================
-- Exploratory Data Analysis
-- ======================
SELECT * FROM spotify;
SELECT COUNT(*) FROM spotify;
SELECT COUNT(DISTINCT artist) FROM spotify;
SELECT COUNT(DISTINCT album) FROM spotify;
SELECT DISTINCT album_type FROM spotify;
SELECT MAX(duration_min) FROM spotify;
SELECT MIN(duration_min) FROM spotify;

-- Clean data by removing tracks with 0 duration
SELECT * FROM spotify WHERE duration_min = 0;
DELETE FROM spotify WHERE duration_min = 0;

SELECT DISTINCT most_played_on FROM spotify;

The project implements queries at three difficulty levels:

-- ======================
-- EASY LEVEL QUERIES
-- ======================

-- 1. Retrieve names of all tracks with >1 billion streams
SELECT track, stream 
FROM spotify
WHERE stream > 1000000000;

-- 2. List all albums with their respective artists
SELECT DISTINCT album, artist 
FROM spotify
ORDER BY artist;

-- 3. Total comments for licensed tracks
SELECT SUM(comments) AS total_comments 
FROM spotify 
WHERE licensed = TRUE;

-- 4. Find all tracks that are singles
SELECT track, album_type 
FROM spotify
WHERE album_type = 'single';

-- 5. Count tracks by each artist
SELECT artist, COUNT(*) AS track_count 
FROM spotify
GROUP BY artist
ORDER BY track_count DESC;


-- ======================
-- MEDIUM LEVEL QUERIES
-- ======================

-- 1. Average danceability by album
SELECT album, ROUND(AVG(danceability), 2) AS avg_danceability
FROM spotify 
GROUP BY album
ORDER BY avg_danceability DESC;

-- 2. Top 5 tracks by energy
SELECT track, energy 
FROM spotify
ORDER BY energy DESC
LIMIT 5;

-- 3. Video track performance
SELECT track, views, likes
FROM spotify
WHERE official_video = TRUE
ORDER BY views DESC;

-- 4. Total views per album
SELECT album, SUM(views) AS total_views
FROM spotify
GROUP BY album
ORDER BY total_views DESC;

-- 5. Tracks more streamed on Spotify than YouTube
SELECT track, 
       SUM(CASE WHEN most_played_on = 'Spotify' THEN stream ELSE 0 END) AS spotify_streams,
       SUM(CASE WHEN most_played_on = 'Youtube' THEN stream ELSE 0 END) AS youtube_streams
FROM spotify
GROUP BY track
HAVING SUM(CASE WHEN most_played_on = 'Spotify' THEN stream ELSE 0 END) > 
       SUM(CASE WHEN most_played_on = 'Youtube' THEN stream ELSE 0 END);


-- ======================
-- ADVANCED LEVEL QUERIES
-- ======================

-- 1. Top 3 most-viewed tracks per artist (Window function)
WITH artist_ranking AS (
    SELECT 
        artist,
        track,
        views,
        DENSE_RANK() OVER (PARTITION BY artist ORDER BY views DESC) AS rank
    FROM spotify
)
SELECT artist, track, views
FROM artist_ranking
WHERE rank <= 3;

-- 2. Tracks with above-average liveness
SELECT track, liveness
FROM spotify 
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
ORDER BY liveness DESC;

-- 3. Energy variation by album (CTE)
WITH album_energy AS (
    SELECT 
        album,
        MAX(energy) AS max_energy,
        MIN(energy) AS min_energy
    FROM spotify
    GROUP BY album
)
SELECT 
    album,
    max_energy - min_energy AS energy_variation
FROM album_energy
ORDER BY energy_variation DESC;

-- 4. High energy-to-liveness ratio tracks
SELECT 
    track,
    energy,
    liveness,
    ROUND(energy/NULLIF(liveness, 0), 2) AS energy_liveness_ratio
FROM spotify
WHERE energy/NULLIF(liveness, 0) > 1.2
ORDER BY energy_liveness_ratio DESC;

-- 5. Cumulative likes by views (Window function)
SELECT 
    track,
    views,
    likes,
    SUM(likes) OVER (ORDER BY views ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_likes
FROM spotify
WHERE likes IS NOT NULL AND views IS NOT NULL
ORDER BY views;

##Technical Implementation
Component	Details
Database System	PostgreSQL
Tools Used	pgAdmin 4, query analysis tools
Key Skills Demonstrated	Database design, Complex SQL querying, Query optimization, Performance analysis


##How to Contribute
Contributions to enhance this project are welcome through:

GitHub forks and pull requests

Issue reporting

Performance improvement suggestions

##License
This project is available under the MIT License.
