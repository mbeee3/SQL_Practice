# Learning SQL for Dotmatics

# Tutorial Video notes
---
# [Web Dev Simplified Learn SQL in 60 Minutes](https://www.youtube.com/watch?v=p3qvj9hO_Bo&list=PLZlA0Gpn_vH9lZBruSHgs41ldy5gZgknU&index=2)

### Intro to syntax and setup
- downloaded and opened MySQL Workbench
- open a new SQL tab from top left
- several keywords off the bat: select, where, from...
 - all keywords are not case-sensitive:
 `sELeCt` is just as good as `SELECT` or `select`
- Recommended to do a few things: always end each sql statement with a semicolon -- not required but it automatically separates queries in a single file, which is useful and common.
 - always write your keywords in all uppercase to distinguish them from table names and column names
 `SELECT * FROM table;` first example line of an SQL query
 strings are denoted by `'string'`
 
 ### my first database
 Example database creation using the create database command:
`CREATE DATABASE test;`
To run, lighnting bolt runs everything in the file, lightning bolt + cursor runs highlighted query

running this line didn't print anything -- look at the 'schemas' pane on the left side and click refresh to make sure it worked. This database is empty -- tables, views, stored procedures, functions are all empty

let's drop the database with 
`DROP DATABASE test;` bada boom it's gone. Don't really use this because it's rarely useful but nice to know just in case

Now that test is gone, create the real database:
`CREATE DATABASE record_company;
USE record_company;` this line sets the database we're querying from as the one we just created
`CREATE TABLE test (
    test_column INT
    );` gives just one column, int names the dtype of the table
Sometimes we don't want to re-create the table though, since there's already data in the table: `ADD another_column VARCHAR(255);` this VARCHAR keyword is used to add a string column with a max length of 255 characters -- variable length character array
Line breaks don't matter! all that stops the 'run' command is a semicolon
`DROP TABLE test` is a useful one too

 ---
Alright now that we've run some test tables and test databases, here's the real table in our database record_company:
`CREATE DATABASE record_company;
USE record_company;
CREATE TABLE bands (
name VARCHAR(255) NOT NULL
);` the column name is name, denoted by lowercase
Adding those keywords NOT NULL at the end forces the table to always assign a name to bands in our table, else it will throw an error and prevent us from adding junk into our table
Let's add ID columns too -- and that's a good habit for any table:
`id INT NOT NULL AUTO_INCREMENT,` does a few things, it sets the dtype, it forces non-null IDs in the table, and it automatically increments as the id as a sequence.
Finally add a `PRIMARY KEY (id)` line before the semicolon to tell sql that the ID is the main differentiator b/w bands

# Second Ever Table -- Albums
`CREATE TABLE albums (
id INT NOT NULL AUTO_INCREMENT,
name VARCHAR(255) NOT NULL,
release_year INT,` no NOT NULL is necessary. This is because a release-year may not yet be set for a given album yet

Need to reference the band table from inside the Albums table:
`band_id INT NOT NULL,
PRIMARY KEY (id),` Creates a new id column
`FOREIGN KEY (band_id) REFERENCES band(id) `links tables! foreign key references any other table than the table set by CREATE TABLE  `
);`

`INSERT INTO bands (name)
VALUES ('Iron Maiden');
INSERT INTO bands (name)
VALUES ('Deuce'), ('Avenged Sevenfold'), ('Ankor');` Add data to tables with the VALUES keyword + ('band name'), using columns to make a list

# SELECT keyword tricks
`SELECT * FROM bands;` This is the first line that generates an output that I have seen! created a new pane called result grid, and shows the table with id and name
`SELECT * FROM bands LIMIT 2;` LIMIT works like Head() in R
`SELECT name FROM bands` gives only the name column


`You can rename/alias the columns in the SELECT line using AS:
SELECT id AS 'ID', name AS 'Band Name'
FROM bands;`
Really useful for referencing what you alias by more than one name

`SELECT * FROM bands ORDER BY name;` Orders by alphabetical order descending, or add ASC to swap to ascending order

`INSERT INTO albums (name, release_year, band_id)
VALUES ('The Number of the Beast', 1985, 1),
		('Power Slave', 1984, 1),
        ('Nightmare', 2018, 2),
        ('Nightmare', 2010, 3),
        ('Test Album', NULL, 3);` It's useful to be able to add values to multiple columns at once! add as many columns as you like to the parentheses after the INSERT keyword to add data for the whole row at once

`SELECT name FROM albums;` allows us to cut duplicates from what's printed.

To update,
`UPDATE albums
SET release_year = 1982 WHERE id =1;` WHERE is super useful for any sort of filtering language. This let us select the album name by unique id, in this case, but we also could have selected by band_id or name if applicable to this update.

`SELECT * FROM albums
WHERE release_year <2000;`
Another use case of WHERE using <,>
We can also use wildcards and LIKE statements like:
`SELECT * FROM albums
WHERE name LIKE '%er%';` The %% searches within the string for those letters. REGEX stuff. Another useful keyword is OR:
`SELECT * FROM albums
WHERE name LIKE '%er%' OR band_id = 2;`

You can use AND to combine filter statements:
`SELECT * FROM albums
WHERE release_year = 1984 AND band_id = 1;`
Or you can use Between --- AND to select between values:
`SELECT * FROM albums
WHERE release_year BETWEEN 2000 AND 2018;`

We can use WHERE to filter for NULLS
`SELECT * FROM albums
WHERE release_year IS NULL;
DELETE FROM albums WHERE id = 5`

Now we've covered the four main SQL data interactions: creating it, reading it, updating it and deleting it. SQL can do a few more really powerful things: Joins!

# Joins
 
 `SELECT * FROM bands
JOIN albums ON bands.id = albums.band_id` First select the table you want to join to, then the table.column you want to add, set equal to the band_id (the column in the table we're joining from)
This lets us create powerful relational queries!

There are more specific types of joins, mainly inner, left, and right:
Inner join does the basic join -- combines data where the data from bands.id - albums.band_id is present on both sides, but left joins with all data present on the left, and right on all data present on the right. You need to think aobut the order you're joining in to make the most use of this function, and it may be issues with how this is set up that's giving us issues in dotmatics
For example, LEFT JOIN also gives rows 4 and 5 ANKOR and Iron Maiden, not particularly useful. Could also do:
`SELECT * FROM albums
RIGHT JOIN bands ON bands.id = albums.band_id;`

Inner joins are more specific, left joins are for all the left and right if they exist. Right joins are less useful overall, since we're mostly interested in the table we're joining to!

 # Aggregate Functions
 
`SELECT AVG(release_year) FROM albums;` to find average release year. More useful than SUM()

Also very useful: COUNT(), MIN(), MAX()
Using COUNT with GROUP BY allows us to get counts for each band:
`SELECT band_id, COUNT(band_id) FROM albums
GROUP BY band_id;`
 
GROUP BY + JOIN is the most complicated syntax to practice -- repository of exercises found here: [Exercises Worksheet Repository](https://github.com/WebDevSimplified/Learn-SQL)

One such example, putting together aliasing, FROM ... AS ..., SELECT, JOIN and COUNT:

`SELECT b.name AS band_name, COUNT(a.id) AS num_albums
FROM bands as b
LEFT JOIN albums as a ON b.id = a.band_id
GROUP BY b.id;` this gives a table with even the bands that don't have albums, and groups them by count of the albums column. The aliases given in the query make things simpler to easier to understand

HAVING is useful for after the GROUP BY line. A WHERE statement won't work in the example above because the where