# Data Modeling with Postgres

## Project Overview
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team is particularly interested in understanding what songs users are listening to. Currently, they don't have an easy way to query their data, which resides in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.
They'd like a data engineer to create a Postgres database with tables designed to optimize queries on song play analysis, and bring you on the project. Your role is to create a database schema and ETL pipeline for this analysis. You'll be able to test your database and ETL pipeline by running queries given to you by the analytics team from Sparkify and compare your results with their expected results.

## Project Description
In this project, you'll apply what you've learned on data modeling with Postgres and build an ETL pipeline using Python. To complete the project, you will need to define fact and dimension tables for a star schema for a particular analytic focus, and write an ETL pipeline that transfers data from files in two local directories into these tables in Postgres using Python and SQL.

## Schema
### Fact Table
**songplays** - Records in log data associated with song plays i.e. records with page NextSong)
- songplay_id BIGSERIAL PRIMARY KEY, _(uniquely indentifies a song play)_
- start_time TIMESTAMP, _(start time of user song play. Foriegn key of time table)_
- user_id INT, _(Id of the user in the system)_
- level TEXT NOT NULL, _(level of the user)_
- song_id TEXT, _(Id of the song in the sytem)_
- artist_id TEXT, _(Id of the artist in the sytem)_
- session_id INT NOT NULL, _(Id of the user session)_
- location TEXT NOT NULL, _(Location of the user)_
- user_agent TEXT NOT NULL _(User agent userd by the user to play the song)_

_songplay_id_ is the primary key. It is chosen as primary key because no other column in this table can unqiuely identify a record. 

### Dimension Tables
**users**  - users in the app
- user_id SERIAL PRIMARY KEY,
- first_name TEXT NOT NULL, 
- last_name TEXT NOT NULL,
- gender TEXT NOT NULL,
- level TEXT NOT NULL
        
_user_id_ is chosen as primary key because it will unique indentify a user in the system. No two users can share same user_id.<br />
**songs**  - songs in music database
- song_id TEXT PRIMARY KEY, 
- title TEXT NOT NULL, 
- artist_id TEXT, 
- year INT NOT NULL, 
- duration NUMERIC _(Duration of the song in milliseconds)_

_song_id_ is chosen as primary key due to the same reason as stated in the users table..<br />
**artists**  - artists in music database
- artist_id TEXT PRIMARY KEY, 
- name TEXT NOT NULL, 
- location TEXT NOT NULL, 
- latitude NUMERIC, 
- longitude NUMERIC

_artist_id_ is chosen as primary key due to the same reason as stated in the users table.<br />

**time**  - timestamps of records in  **songplays**  broken down into specific units
- start_time TIMESTAMP PRIMARY KEY, 
- hour INT, 
- day INT, 
- week INT, 
- month INT, 
- year INT, 
- weekday TEXT

_start_time_ is selected as primary key because it represents a unqiue instance of time.

## Description of Quries
### Create Tables
These queries are pretty straight forward and create table as defined in the schema section above.

### Insert Quries
There are seperate insert quries for each table. More precisely, these are upsert queries. The quries will insert new records in the corresponding table but if there is conflict on primary key value, it will simply ingore that insert. However, for the users table insert, it will update the level of the user if there is a conflict. `EXCLUDED` table is used in the `ON CONFLICT UPDATE` part of the `UPSERT` statement to get the new value being inserted.

### Find Songs Query
This is a little complicated query as compared to other ones in the project. It selects `song_id` and `artist_id` by JOINing the `songs` and `artists` tables ON `artist_id`. Both of these tables have `artist_id` as a common attribute which makes it easy to JOIN these tables. The problem this query is solving is that there is no information about `artist_id` and `song_id` in the log file so we had to JOIN these two tables to get the required information (to insert in songplays table) based on `title`, `artist_name` and `duration`.


## Description of Project Files
`data` contains data about songs and logs of song plays.<br />
`test.ipynb` displays the first few rows of each table.<br />
`create_tables.py` drops and creates tables. Run this file to reset tables before running ETL scripts.<br />
`etl.ipynb` reads and processes a single file from `song_data` and `log_data` and loads the data into tables.<br />
`etl.py` reads and processes files from `song_data` and `log_data` and loads them into tables.<br />
`sql_queries.py` contains all sql queries, and is imported into the last three files above.<br />
`README.md` contains discussion about the project.




